---
title: Статические файлы в ASP.NET Core
author: rick-anderson
description: Узнайте, как в веб-приложениях ASP.NET Core обслуживать и защищать статические файлы, а также как настраивать ПО промежуточного слоя по размещению статических файлов.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 39c5c5d4875e1d59abaa6d998a09dbffd723214d
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793410"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="486b1-103">Статические файлы в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="486b1-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="486b1-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="486b1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="486b1-105">Статические файлы, такие как HTML, CSS, изображения и JavaScript, являются ресурсами, которые приложения ASP.NET Core предоставляют клиентам напрямую по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="486b1-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="486b1-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples/3x) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="486b1-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples/3x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="486b1-107">Обслуживание статических файлов</span><span class="sxs-lookup"><span data-stu-id="486b1-107">Serve static files</span></span>

<span data-ttu-id="486b1-108">Статические файлы хранятся в [корневом каталоге документов](xref:fundamentals/index#web-root) проекта.</span><span class="sxs-lookup"><span data-stu-id="486b1-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="486b1-109">Каталог по умолчанию — `{content root}/wwwroot`, но его можно изменить с помощью метода [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_).</span><span class="sxs-lookup"><span data-stu-id="486b1-109">The default directory is `{content root}/wwwroot`, but it can be changed with the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="486b1-110">См. разделы [Корневой каталог содержимого](xref:fundamentals/index#content-root) и [Корневой веб-каталог](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="486b1-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="486b1-111">Метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> устанавливает текущий каталог в качестве корневого каталога содержимого:</span><span class="sxs-lookup"><span data-stu-id="486b1-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/Program2.cs?name=snippet_Main)]

<span data-ttu-id="486b1-112">Предыдущий код создан с помощью шаблона веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="486b1-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="486b1-113">Статические файлы доступны по относительному пути от [корневого каталога документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="486b1-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="486b1-114">Например, шаблоны проекта **Web Application** содержат несколько папок в папке `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="486b1-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="486b1-115">Мы рекомендуем создать папку *wwwroot/images* и добавить в нее файл *wwwroot/images/MyImage.jpg*.</span><span class="sxs-lookup"><span data-stu-id="486b1-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="486b1-116">Формат URI для доступа к файлу в папке `images`: `https://<hostname>/images/<image_file_name>`.</span><span class="sxs-lookup"><span data-stu-id="486b1-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="486b1-117">Например: `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="486b1-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="486b1-118">Обслуживание файлов в корневом каталоге веб-сайта</span><span class="sxs-lookup"><span data-stu-id="486b1-118">Serve files in web root</span></span>

<span data-ttu-id="486b1-119">Шаблоны веб-приложений по умолчанию вызывают метод <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> в `Startup.Configure`, который позволяет обслуживать статические файлы:</span><span class="sxs-lookup"><span data-stu-id="486b1-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/Startup.cs?name=snippet&highlight=14)]

<span data-ttu-id="486b1-120">Эта перегрузка метода `UseStaticFiles` не принимает параметров, она помечает файлы в [корневом каталоге документов](xref:fundamentals/index#web-root) как обслуживаемые.</span><span class="sxs-lookup"><span data-stu-id="486b1-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="486b1-121">Следующая разметка ссылается на *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="486b1-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg"  class="img" alt="My image." />
```

<span data-ttu-id="486b1-122">В приведенном выше коде знак тильды `~/` указывает на [корневой каталог документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="486b1-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="486b1-123">Обслуживание файлов вне корневого веб-каталога</span><span class="sxs-lookup"><span data-stu-id="486b1-123">Serve files outside of web root</span></span>

<span data-ttu-id="486b1-124">Пусть имеется иерархия каталогов, в которой статические файлы обслуживаются вне [корневого каталога документов](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="486b1-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="486b1-125">В запросе можно получить доступ к файлу `red-rose.jpg`, настроив ПО промежуточного слоя для статических файлов следующим образом:</span><span class="sxs-lookup"><span data-stu-id="486b1-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupRose.cs?name=snippet&highlight=14-21)]

<span data-ttu-id="486b1-126">В приведенном выше коде доступ к иерархии каталога *MyStaticFiles* представляется через сегмент URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="486b1-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="486b1-127">Запрос к `https://<hostname>/StaticFiles/images/red-rose.jpg` обслуживает файл *red-rose.jpg*.</span><span class="sxs-lookup"><span data-stu-id="486b1-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="486b1-128">Следующая разметка ссылается на *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="486b1-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose." />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="486b1-129">Установка заголовков HTTP-ответов</span><span class="sxs-lookup"><span data-stu-id="486b1-129">Set HTTP response headers</span></span>

<span data-ttu-id="486b1-130">Для установки заголовков HTTP-ответов можно использовать объект <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="486b1-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="486b1-131">Кроме настройки обслуживания статических файлов в [корневом каталоге документов](xref:fundamentals/index#web-root), следующий код также устанавливает заголовок `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="486b1-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupAddHeader.cs?name=snippet&highlight=14-23)]

<span data-ttu-id="486b1-132">Статические файлы являются общедоступными для кэширования в течение 600 с:</span><span class="sxs-lookup"><span data-stu-id="486b1-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Добавлены заголовки ответов, включая заголовок Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="486b1-134">Авторизация статических файлов</span><span class="sxs-lookup"><span data-stu-id="486b1-134">Static file authorization</span></span>

<span data-ttu-id="486b1-135">ПО промежуточного слоя для статических файлов не предоставляет возможности авторизации.</span><span class="sxs-lookup"><span data-stu-id="486b1-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="486b1-136">Все обслуживаемые им файлы, включая расположенные в `wwwroot`, находятся в открытом доступе.</span><span class="sxs-lookup"><span data-stu-id="486b1-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="486b1-137">Для обслуживания файлов с авторизацией:</span><span class="sxs-lookup"><span data-stu-id="486b1-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="486b1-138">Сохраните файлы за пределами каталога `wwwroot` в любом каталоге, к которому имеет доступ ПО промежуточного слоя для статических файлов.</span><span class="sxs-lookup"><span data-stu-id="486b1-138">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="486b1-139">Обслуживайте их через метод действия, к которому применима авторизация, и получите объект [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult):</span><span class="sxs-lookup"><span data-stu-id="486b1-139">Serve them via an action method to which authorization is applied and return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="directory-browsing"></a><span data-ttu-id="486b1-140">Просмотр каталогов</span><span class="sxs-lookup"><span data-stu-id="486b1-140">Directory browsing</span></span>

<span data-ttu-id="486b1-141">Просмотр каталогов позволяет просматривать список каталогов в указанных каталогах.</span><span class="sxs-lookup"><span data-stu-id="486b1-141">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="486b1-142">По соображениям безопасности просмотр каталогов отключен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="486b1-142">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="486b1-143">См. раздел [Безопасность статических файлов](#sc).</span><span class="sxs-lookup"><span data-stu-id="486b1-143">For more information, see [Considerations](#sc).</span></span>

<span data-ttu-id="486b1-144">Включите просмотр каталогов с помощью:</span><span class="sxs-lookup"><span data-stu-id="486b1-144">Enable directory browsing with:</span></span>

* <span data-ttu-id="486b1-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> в `Startup.ConfigureServices`;</span><span class="sxs-lookup"><span data-stu-id="486b1-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="486b1-146">[UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="486b1-146">[UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupBrowse.cs?name=snippet&highlight=4,20-34)]

<span data-ttu-id="486b1-147">Приведенный выше код разрешает просмотр папки *wwwroot/images* с помощью URL-адреса `https://<hostname>/MyImages`, со ссылками на все файлы и папки:</span><span class="sxs-lookup"><span data-stu-id="486b1-147">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![просмотр каталогов](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="486b1-149">Обслуживание документов по умолчанию</span><span class="sxs-lookup"><span data-stu-id="486b1-149">Serve default documents</span></span>

<span data-ttu-id="486b1-150">Страница по умолчанию является для пользователей отправной точкой на веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="486b1-150">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="486b1-151">Чтобы обслуживать страницу по умолчанию из `wwwroot` без полного URI, вызовите метод <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A>:</span><span class="sxs-lookup"><span data-stu-id="486b1-151">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty.cs?name=snippet&highlight=14)]

<span data-ttu-id="486b1-152">Для обслуживания файла по умолчанию метод `UseDefaultFiles`должен быть вызван до метода `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="486b1-152">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="486b1-153">`UseDefaultFiles` — это средство переопределения URL-адресов, которое не обслуживает файл.</span><span class="sxs-lookup"><span data-stu-id="486b1-153">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="486b1-154">При использовании `UseDefaultFiles` запросы к папке в `wwwroot` будут искать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="486b1-154">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="486b1-155">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="486b1-155">*default.htm*</span></span>
* <span data-ttu-id="486b1-156">*default.html*</span><span class="sxs-lookup"><span data-stu-id="486b1-156">*default.html*</span></span>
* <span data-ttu-id="486b1-157">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="486b1-157">*index.htm*</span></span>
* <span data-ttu-id="486b1-158">*index.html*</span><span class="sxs-lookup"><span data-stu-id="486b1-158">*index.html*</span></span>

<span data-ttu-id="486b1-159">Первый найденный файл из списка будет обслужен, как будто был введен полный URI.</span><span class="sxs-lookup"><span data-stu-id="486b1-159">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="486b1-160">URL-адрес в браузере будет соответствовать запрошенному URI.</span><span class="sxs-lookup"><span data-stu-id="486b1-160">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="486b1-161">Следующий код позволяет изменить имя файла по умолчанию на *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="486b1-161">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupDefault.cs?name=snippet2)]

<span data-ttu-id="486b1-162">В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="486b1-162">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupDefault.cs?name=snippet)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="486b1-163">Использование UseFileServer для документов по умолчанию</span><span class="sxs-lookup"><span data-stu-id="486b1-163">UseFileServer for default documents</span></span>

<span data-ttu-id="486b1-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> объединяет функции `UseStaticFiles`, `UseDefaultFiles` и при необходимости `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="486b1-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="486b1-165">Вызовите `app.UseFileServer`, чтобы включить обслуживание статических файлов и файла по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="486b1-165">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="486b1-166">Просмотр каталогов отключен.</span><span class="sxs-lookup"><span data-stu-id="486b1-166">Directory browsing isn't enabled.</span></span> <span data-ttu-id="486b1-167">В следующем примере кода демонстрируется `Startup.Configure` с `UseFileServer`:</span><span class="sxs-lookup"><span data-stu-id="486b1-167">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty2.cs?name=snippet&highlight=14)]

<span data-ttu-id="486b1-168">Следующий пример кода позволяет обслуживать статические файлы, файл по умолчанию и просмотр каталогов:</span><span class="sxs-lookup"><span data-stu-id="486b1-168">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="486b1-169">В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="486b1-169">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty3.cs?name=snippet&highlight=14-18)]

<span data-ttu-id="486b1-170">Пусть имеется следующая иерархия каталогов:</span><span class="sxs-lookup"><span data-stu-id="486b1-170">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="486b1-171">Следующий пример кода позволяет обслуживать статические файлы, файл по умолчанию и просмотр каталогов `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="486b1-171">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupUseFileServer.cs?name=snippet&highlight=4,20-30)]

<span data-ttu-id="486b1-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> должно вызываться, когда свойство `EnableDirectoryBrowsing` имеет значение `true`.</span><span class="sxs-lookup"><span data-stu-id="486b1-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="486b1-173">При указанных выше коде и иерархии файлов URL-адреса будут разрешаться следующим образом:</span><span class="sxs-lookup"><span data-stu-id="486b1-173">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="486b1-174">URI</span><span class="sxs-lookup"><span data-stu-id="486b1-174">URI</span></span>            |      <span data-ttu-id="486b1-175">Ответ</span><span class="sxs-lookup"><span data-stu-id="486b1-175">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="486b1-176">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="486b1-176">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="486b1-177">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="486b1-177">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="486b1-178">Если в каталоге *MyStaticFiles* отсутствует файл с именем по умолчанию, `https://<hostname>/StaticFiles` возвращает список содержимого каталога с доступными для перехода ссылками:</span><span class="sxs-lookup"><span data-stu-id="486b1-178">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Список статических файлов](static-files/_static/db2.png)

<span data-ttu-id="486b1-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> и <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> выполняют перенаправление на стороне клиента из целевого URI без `/` в конце в целевой URI с `/` в конце.</span><span class="sxs-lookup"><span data-stu-id="486b1-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="486b1-181">Например, из `https://<hostname>/StaticFiles` в `https://<hostname>/StaticFiles/`.</span><span class="sxs-lookup"><span data-stu-id="486b1-181">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="486b1-182">Относительные URL-адреса в каталоге *StaticFiles* считаются недопустимыми без косой черты в конце (`/`).</span><span class="sxs-lookup"><span data-stu-id="486b1-182">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="486b1-183">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="486b1-183">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="486b1-184">Класс [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) содержит свойство `Mappings`, которое используется для сопоставления расширений файлов и типов содержимого MIME.</span><span class="sxs-lookup"><span data-stu-id="486b1-184">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="486b1-185">В следующем примере несколько расширений файлов сопоставляются с известными типами MIME.</span><span class="sxs-lookup"><span data-stu-id="486b1-185">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="486b1-186">Расширение *.rtf* заменяется, а *.mp4* удаляется:</span><span class="sxs-lookup"><span data-stu-id="486b1-186">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupFileExtensionContentTypeProvider.cs?name=snippet2)]

<span data-ttu-id="486b1-187">В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="486b1-187">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupFileExtensionContentTypeProvider.cs?name=snippet&highlight=14-42)]

<span data-ttu-id="486b1-188">См. раздел [Типы содержимого MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="486b1-188">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="486b1-189">Нестандартные типы содержимого</span><span class="sxs-lookup"><span data-stu-id="486b1-189">Non-standard content types</span></span>

<span data-ttu-id="486b1-190">ПО промежуточного слоя для статических файлов распознает почти 400 известных типов содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="486b1-190">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="486b1-191">Если пользователь запрашивает файл неизвестного типа, ПО промежуточного слоя статических файлов передает запрос следующему компоненту ПО промежуточного слоя в конвейере.</span><span class="sxs-lookup"><span data-stu-id="486b1-191">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="486b1-192">Если ПО промежуточного слоя не удается обработать запрос, возвращается ответ *404 Не найдено*.</span><span class="sxs-lookup"><span data-stu-id="486b1-192">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="486b1-193">Если просмотр каталогов разрешен, то в списке каталогов отображается ссылка на файл.</span><span class="sxs-lookup"><span data-stu-id="486b1-193">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="486b1-194">Следующий код включает обслуживание неизвестных типов и обслуживает неизвестные файлы как изображения:</span><span class="sxs-lookup"><span data-stu-id="486b1-194">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupServeUnknownFileTypes.cs?name=snippet2)]

<span data-ttu-id="486b1-195">В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="486b1-195">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupServeUnknownFileTypes.cs?name=snippet&highlight=14-18)]

<span data-ttu-id="486b1-196">При выполнении вышеописанного кода ответ на запрос файла с неизвестным типом содержимого вернется в виде изображения.</span><span class="sxs-lookup"><span data-stu-id="486b1-196">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="486b1-197">Включение параметра [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) представляет угрозу безопасности.</span><span class="sxs-lookup"><span data-stu-id="486b1-197">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="486b1-198">По умолчанию он отключен, и его использование не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="486b1-198">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="486b1-199">Использование класса [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) является более безопасной альтернативой для обслуживания файлов с нестандартными расширениями.</span><span class="sxs-lookup"><span data-stu-id="486b1-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="486b1-200">Предоставление файлов из нескольких расположений</span><span class="sxs-lookup"><span data-stu-id="486b1-200">Serve files from multiple locations</span></span>

<span data-ttu-id="486b1-201">В качестве значений `UseStaticFiles` и `UseFileServer` по умолчанию используется поставщик файлов, указывающий на `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="486b1-201">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="486b1-202">Вы можете предоставить дополнительные экземпляры `UseStaticFiles` и `UseFileServer` с другими поставщиками файлов для обслуживания файлов из других расположений.</span><span class="sxs-lookup"><span data-stu-id="486b1-202">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="486b1-203">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="486b1-203">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="486b1-204">Безопасность статических файлов</span><span class="sxs-lookup"><span data-stu-id="486b1-204">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="486b1-205">Использование `UseDirectoryBrowser` и `UseStaticFiles` может привести к утечке конфиденциальной информации.</span><span class="sxs-lookup"><span data-stu-id="486b1-205">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="486b1-206">Настоятельно рекомендуется отключать просмотр каталогов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="486b1-206">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="486b1-207">Тщательно проверьте, просмотр каких каталогов разрешен посредством `UseStaticFiles` или `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="486b1-207">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="486b1-208">Весь каталог и его подкаталоги становятся общедоступными.</span><span class="sxs-lookup"><span data-stu-id="486b1-208">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="486b1-209">Храните файлы, предназначенные для общего доступа, в выделенных каталогах, таких как `<content_root>/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="486b1-209">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="486b1-210">Отделите эти файлы от представлений MVC, Razor Pages, файлов конфигурации и т. д.</span><span class="sxs-lookup"><span data-stu-id="486b1-210">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="486b1-211">К URL-адресам содержимого, к которому предоставлен доступ методами `UseDirectoryBrowser` и `UseStaticFiles`, применяются те же требования по регистрозависимости и запрещенным символам, что и к базовой файловой системе.</span><span class="sxs-lookup"><span data-stu-id="486b1-211">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="486b1-212">Например, в Windows регистр не учитывается, а в macOS и Linux учитывается.</span><span class="sxs-lookup"><span data-stu-id="486b1-212">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="486b1-213">Приложения ASP.NET Core, размещенные в IIS, используют [Модуль Core ASP.NET](xref:host-and-deploy/aspnet-core-module) для перенаправления всех запросов к приложению, включая запросы статических файлов.</span><span class="sxs-lookup"><span data-stu-id="486b1-213">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="486b1-214">Обработчик статических файлов IIS не используется и не выполняет обработку запросов.</span><span class="sxs-lookup"><span data-stu-id="486b1-214">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="486b1-215">Выполните следующие шаги в диспетчере служб IIS для удаления обработчика статических файлов IIS на уровне сервера или веб-сайта:</span><span class="sxs-lookup"><span data-stu-id="486b1-215">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="486b1-216">Перейдите к компоненту **Модули**.</span><span class="sxs-lookup"><span data-stu-id="486b1-216">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="486b1-217">Выберите в списке модуль **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="486b1-217">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="486b1-218">Нажмите кнопку **Удалить** в боковой панели **Действия**.</span><span class="sxs-lookup"><span data-stu-id="486b1-218">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="486b1-219">Если обработчик статических файлов IIS включен **и** модуль ASP.NET Core настроен неправильно, то статические файлы будут обслуживаться.</span><span class="sxs-lookup"><span data-stu-id="486b1-219">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="486b1-220">Это может случиться, если, например, не был развернут файл *web.config*.</span><span class="sxs-lookup"><span data-stu-id="486b1-220">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="486b1-221">Размещайте файлы с кодом, включая файлы *.cs* и *.cshtml*, за пределами [корневого веб-каталога](xref:fundamentals/index#web-root) проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="486b1-221">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="486b1-222">Таким образом, в приложении создается логическое разделение между клиентским содержимым и серверным кодом.</span><span class="sxs-lookup"><span data-stu-id="486b1-222">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="486b1-223">Это предотвращает утечку серверного кода.</span><span class="sxs-lookup"><span data-stu-id="486b1-223">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="486b1-224">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="486b1-224">Additional resources</span></span>

* [<span data-ttu-id="486b1-225">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="486b1-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="486b1-226">Введение в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="486b1-226">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="486b1-227">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="486b1-227">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="486b1-228">Статические файлы, такие как HTML, CSS, изображения и JavaScript, являются ресурсами, которые приложения ASP.NET Core предоставляют клиентам напрямую.</span><span class="sxs-lookup"><span data-stu-id="486b1-228">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="486b1-229">Для обслуживания таких файлов требуется настроить некоторые параметры.</span><span class="sxs-lookup"><span data-stu-id="486b1-229">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="486b1-230">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="486b1-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="486b1-231">Обслуживание статических файлов</span><span class="sxs-lookup"><span data-stu-id="486b1-231">Serve static files</span></span>

<span data-ttu-id="486b1-232">Статические файлы хранятся в [корневом каталоге документов](xref:fundamentals/index#web-root) проекта.</span><span class="sxs-lookup"><span data-stu-id="486b1-232">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="486b1-233">Каталог по умолчанию — *{корневой_каталог_содержимого}/wwwroot*, но его можно изменить через метод [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_).</span><span class="sxs-lookup"><span data-stu-id="486b1-233">The default directory is *{content root}/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="486b1-234">Дополнительные сведения см. в разделах [Корневой каталог содержимого](xref:fundamentals/index#content-root) и [Корневой веб-каталог](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="486b1-234">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="486b1-235">Веб-узел приложения должен знать о расположении корневого каталога содержимого.</span><span class="sxs-lookup"><span data-stu-id="486b1-235">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="486b1-236">Метод `WebHost.CreateDefaultBuilder` устанавливает текущий каталог в качестве корневого каталога содержимого:</span><span class="sxs-lookup"><span data-stu-id="486b1-236">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="486b1-237">Статические файлы доступны по относительному пути от [корневого каталога документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="486b1-237">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="486b1-238">Например, шаблон проекта **Web Application** содержит несколько папок в папке `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="486b1-238">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="486b1-239">Формат URI для доступа к файлу во вложенной папке *images*: *http://\<server_address>/images/\<image_file_name>* .</span><span class="sxs-lookup"><span data-stu-id="486b1-239">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="486b1-240">Например, *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="486b1-240">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="486b1-241">Если код предназначен для .NET Framework, добавьте в проект пакет [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/).</span><span class="sxs-lookup"><span data-stu-id="486b1-241">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="486b1-242">Если код предназначен для .NET Core, то этот пакет уже включен в метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="486b1-242">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="486b1-243">Настройте [ПО промежуточного слоя](xref:fundamentals/middleware/index), позволяющее обслуживать статические файлы.</span><span class="sxs-lookup"><span data-stu-id="486b1-243">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="486b1-244">Обслуживание файлов в корневом веб-каталоге</span><span class="sxs-lookup"><span data-stu-id="486b1-244">Serve files inside of web root</span></span>

<span data-ttu-id="486b1-245">Вызовите метод [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="486b1-245">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="486b1-246">Эта перегрузка метода `UseStaticFiles` не принимает параметров, она помечает файлы в [корневом каталоге документов](xref:fundamentals/index#web-root) как обслуживаемые.</span><span class="sxs-lookup"><span data-stu-id="486b1-246">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="486b1-247">Следующая разметка ссылается на *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="486b1-247">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="486b1-248">В приведенном выше коде знак тильды `~/` указывает на [корневой каталог документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="486b1-248">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="486b1-249">Обслуживание файлов вне корневого веб-каталога</span><span class="sxs-lookup"><span data-stu-id="486b1-249">Serve files outside of web root</span></span>

<span data-ttu-id="486b1-250">Пусть имеется иерархия каталогов, в которой статические файлы обслуживаются вне [корневого каталога документов](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="486b1-250">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="486b1-251">В запросе можно получить доступ к файлу *banner1.svg*, настроив ПО промежуточного слоя для статических файлов следующим образом:</span><span class="sxs-lookup"><span data-stu-id="486b1-251">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="486b1-252">В приведенном выше коде доступ к иерархии каталога *MyStaticFiles* представляется через сегмент URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="486b1-252">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="486b1-253">Запрос *http://\<server_address>/StaticFiles/images/banner1.svg* обслуживает файл *banner1.svg*.</span><span class="sxs-lookup"><span data-stu-id="486b1-253">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="486b1-254">Следующая разметка ссылается на *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="486b1-254">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="486b1-255">Установка заголовков HTTP-ответов</span><span class="sxs-lookup"><span data-stu-id="486b1-255">Set HTTP response headers</span></span>

<span data-ttu-id="486b1-256">Для установки заголовков HTTP-ответов можно использовать объект [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions).</span><span class="sxs-lookup"><span data-stu-id="486b1-256">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="486b1-257">Кроме настройки обслуживания статических файлов в [корневом каталоге документов](xref:fundamentals/index#web-root), следующий код также устанавливает заголовок `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="486b1-257">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="486b1-258">Метод [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) содержится в пакете [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="486b1-258">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="486b1-259">Файлы, к которым был предоставлен доступ, кэшируются в течение 10 минут (600 секунд) в среде разработки:</span><span class="sxs-lookup"><span data-stu-id="486b1-259">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Добавлены заголовки ответов, включая заголовок Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="486b1-261">Авторизация статических файлов</span><span class="sxs-lookup"><span data-stu-id="486b1-261">Static file authorization</span></span>

<span data-ttu-id="486b1-262">ПО промежуточного слоя для статических файлов не предоставляет возможности авторизации.</span><span class="sxs-lookup"><span data-stu-id="486b1-262">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="486b1-263">Все обслуживаемые им файлы, включая расположенные в *wwwroot*, находятся в открытом доступе.</span><span class="sxs-lookup"><span data-stu-id="486b1-263">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="486b1-264">Для обслуживания файлов с авторизацией:</span><span class="sxs-lookup"><span data-stu-id="486b1-264">To serve files based on authorization:</span></span>

* <span data-ttu-id="486b1-265">Сохраните файлы в любом каталоге за пределами каталога *wwwroot*, к которому имеет доступ ПО промежуточного слоя для статических файлов.</span><span class="sxs-lookup"><span data-stu-id="486b1-265">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="486b1-266">Обслуживайте их через метод действия, к которому применима авторизация.</span><span class="sxs-lookup"><span data-stu-id="486b1-266">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="486b1-267">Возвращайте объект [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult):</span><span class="sxs-lookup"><span data-stu-id="486b1-267">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="486b1-268">Просмотр каталогов</span><span class="sxs-lookup"><span data-stu-id="486b1-268">Enable directory browsing</span></span>

<span data-ttu-id="486b1-269">Просмотр каталогов позволяет пользователям веб-приложениям просматривать файлы и каталоги внутри определенного каталога.</span><span class="sxs-lookup"><span data-stu-id="486b1-269">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="486b1-270">По соображениям безопасности просмотр каталогов отключен по умолчанию (см. раздел [Особенности](#sc)).</span><span class="sxs-lookup"><span data-stu-id="486b1-270">Directory browsing is disabled by default for security reasons (see [Considerations](#sc)).</span></span> <span data-ttu-id="486b1-271">Разрешить просмотр каталогов можно вызвав метод [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="486b1-271">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="486b1-272">Добавьте необходимые службы путем вызова метода <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> из `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="486b1-272">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="486b1-273">Приведенный выше код разрешает просмотр папки *wwwroot/images* с помощью URL-адреса *http://\<server_address>/MyImages*, со ссылками на все файлы и папки:</span><span class="sxs-lookup"><span data-stu-id="486b1-273">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![просмотр каталогов](static-files/_static/dir-browse.png)

<span data-ttu-id="486b1-275">Информацию об угрозе безопасности при включении просмотра каталогов см. в разделе [Особенности](#considerations).</span><span class="sxs-lookup"><span data-stu-id="486b1-275">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="486b1-276">Обратите внимание на два вызова метода `UseStaticFiles` в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="486b1-276">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="486b1-277">Первый вызов включает обслуживание статических файлов в папке *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="486b1-277">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="486b1-278">Второй вызов включает просмотр папки *wwwroot/images* с помощью URL-адреса *http://\<server_address>/MyImages*:</span><span class="sxs-lookup"><span data-stu-id="486b1-278">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="486b1-279">Обслуживание документа по умолчанию</span><span class="sxs-lookup"><span data-stu-id="486b1-279">Serve a default document</span></span>

<span data-ttu-id="486b1-280">Домашняя страница по умолчанию является для посетителей логической отправной точкой при посещении веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="486b1-280">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="486b1-281">Для обслуживания страницы по умолчанию без указания полного имени URI вызовите метод [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) из `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="486b1-281">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="486b1-282">Для обслуживания файла по умолчанию метод `UseDefaultFiles`должен быть вызван до метода `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="486b1-282">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="486b1-283">Метод `UseDefaultFiles` фактически не обслуживает файл, а только перезаписывает URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="486b1-283">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="486b1-284">Для обслуживания файла включите ПО промежуточного слоя для статических файлов, вызвав метод `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="486b1-284">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="486b1-285">При вызове метода `UseDefaultFiles` запросы к папке будут искать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="486b1-285">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="486b1-286">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="486b1-286">*default.htm*</span></span>
* <span data-ttu-id="486b1-287">*default.html*</span><span class="sxs-lookup"><span data-stu-id="486b1-287">*default.html*</span></span>
* <span data-ttu-id="486b1-288">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="486b1-288">*index.htm*</span></span>
* <span data-ttu-id="486b1-289">*index.html*</span><span class="sxs-lookup"><span data-stu-id="486b1-289">*index.html*</span></span>

<span data-ttu-id="486b1-290">Первый найденный файл из списка будет обслужен, как будто был введен полный URI.</span><span class="sxs-lookup"><span data-stu-id="486b1-290">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="486b1-291">URL-адрес в браузере будет соответствовать запрошенному URI.</span><span class="sxs-lookup"><span data-stu-id="486b1-291">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="486b1-292">Следующий код позволяет изменить имя файла по умолчанию на *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="486b1-292">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="486b1-293">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="486b1-293">UseFileServer</span></span>

<span data-ttu-id="486b1-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> объединяет функции `UseStaticFiles`, `UseDefaultFiles` и при необходимости `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="486b1-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="486b1-295">Следующий пример кода позволяет обслуживать статические файлы и файл по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="486b1-295">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="486b1-296">Просмотр каталогов отключен.</span><span class="sxs-lookup"><span data-stu-id="486b1-296">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="486b1-297">Следующий код отличается от предыдущей перегрузки метода без параметров включением просмотра каталогов:</span><span class="sxs-lookup"><span data-stu-id="486b1-297">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="486b1-298">Пусть имеется следующая иерархия каталогов:</span><span class="sxs-lookup"><span data-stu-id="486b1-298">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="486b1-299">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="486b1-299">**wwwroot**</span></span>
  * <span data-ttu-id="486b1-300">**css**</span><span class="sxs-lookup"><span data-stu-id="486b1-300">**css**</span></span>
  * <span data-ttu-id="486b1-301">**images**</span><span class="sxs-lookup"><span data-stu-id="486b1-301">**images**</span></span>
  * <span data-ttu-id="486b1-302">**js**</span><span class="sxs-lookup"><span data-stu-id="486b1-302">**js**</span></span>
* <span data-ttu-id="486b1-303">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="486b1-303">**MyStaticFiles**</span></span>
  * <span data-ttu-id="486b1-304">**images**</span><span class="sxs-lookup"><span data-stu-id="486b1-304">**images**</span></span>
    * <span data-ttu-id="486b1-305">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="486b1-305">*banner1.svg*</span></span>
  * <span data-ttu-id="486b1-306">*default.html*</span><span class="sxs-lookup"><span data-stu-id="486b1-306">*default.html*</span></span>

<span data-ttu-id="486b1-307">Следующий пример кода включает обслуживание статических файлов, файлы по умолчанию и просмотр каталога `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="486b1-307">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="486b1-308">Метод `AddDirectoryBrowser` должен вызываться при значении `true` свойства `EnableDirectoryBrowsing`:</span><span class="sxs-lookup"><span data-stu-id="486b1-308">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="486b1-309">При указанных выше коде и иерархии файлов URL-адреса будут разрешаться следующим образом:</span><span class="sxs-lookup"><span data-stu-id="486b1-309">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="486b1-310">URI</span><span class="sxs-lookup"><span data-stu-id="486b1-310">URI</span></span>            |                             <span data-ttu-id="486b1-311">Ответ</span><span class="sxs-lookup"><span data-stu-id="486b1-311">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="486b1-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="486b1-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="486b1-313">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="486b1-313">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="486b1-314">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="486b1-314">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="486b1-315">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="486b1-315">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="486b1-316">Если в каталоге *MyStaticFiles* отсутствует файл с именем по умолчанию, *http://\<server_address>/StaticFiles* возвращает список содержимого каталога с доступными для перехода ссылками:</span><span class="sxs-lookup"><span data-stu-id="486b1-316">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Список статических файлов](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="486b1-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> и <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> выполняют перенаправление на стороне клиента из `http://{SERVER ADDRESS}/StaticFiles` (без косой черты в конце) в `http://{SERVER ADDRESS}/StaticFiles/` (с косой чертой в конце).</span><span class="sxs-lookup"><span data-stu-id="486b1-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="486b1-319">Относительные URL-адреса в каталоге *StaticFiles* считаются недопустимыми без косой черты в конце.</span><span class="sxs-lookup"><span data-stu-id="486b1-319">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="486b1-320">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="486b1-320">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="486b1-321">Класс [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) содержит свойство `Mappings`, которое служит для сопоставления расширений файлов и типов содержимого MIME.</span><span class="sxs-lookup"><span data-stu-id="486b1-321">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="486b1-322">В следующем примере несколько расширений файлов регистрируются в известные типы MIME.</span><span class="sxs-lookup"><span data-stu-id="486b1-322">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="486b1-323">Расширение *.rtf* заменяется, а *.mp4* удаляется.</span><span class="sxs-lookup"><span data-stu-id="486b1-323">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="486b1-324">См. раздел [Типы содержимого MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="486b1-324">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="486b1-325">Нестандартные типы содержимого</span><span class="sxs-lookup"><span data-stu-id="486b1-325">Non-standard content types</span></span>

<span data-ttu-id="486b1-326">ПО промежуточного слоя для статических файлов воспринимает почти 400 известных типов содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="486b1-326">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="486b1-327">Если пользователь запрашивает файл неизвестного типа, ПО промежуточного слоя статических файлов передает запрос следующему компоненту ПО промежуточного слоя в конвейере.</span><span class="sxs-lookup"><span data-stu-id="486b1-327">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="486b1-328">Если ПО промежуточного слоя не удается обработать запрос, возвращается ответ *404 Не найдено*.</span><span class="sxs-lookup"><span data-stu-id="486b1-328">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="486b1-329">Если просмотр каталогов разрешен, то в списке каталогов отображается ссылка на файл.</span><span class="sxs-lookup"><span data-stu-id="486b1-329">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="486b1-330">Следующий код включает обслуживание неизвестных типов и обслуживает неизвестные файлы как изображения:</span><span class="sxs-lookup"><span data-stu-id="486b1-330">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="486b1-331">При выполнении вышеописанного кода ответ на запрос файла с неизвестным типом содержимого вернется в виде изображения.</span><span class="sxs-lookup"><span data-stu-id="486b1-331">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="486b1-332">Включение параметра [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) представляет угрозу безопасности.</span><span class="sxs-lookup"><span data-stu-id="486b1-332">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="486b1-333">По умолчанию он отключен, и его использование не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="486b1-333">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="486b1-334">Использование класса [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) является более безопасной альтернативой для обслуживания файлов с нестандартными расширениями.</span><span class="sxs-lookup"><span data-stu-id="486b1-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="486b1-335">Предоставление файлов из нескольких расположений</span><span class="sxs-lookup"><span data-stu-id="486b1-335">Serve files from multiple locations</span></span>

<span data-ttu-id="486b1-336">В качестве значений `UseStaticFiles` и `UseFileServer` по умолчанию используется поставщик файлов, указывающий на *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="486b1-336">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="486b1-337">Вы можете предоставить дополнительные экземпляры `UseStaticFiles` и `UseFileServer` с другими поставщиками файлов для предоставления файлов из других расположений.</span><span class="sxs-lookup"><span data-stu-id="486b1-337">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="486b1-338">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="486b1-338">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="486b1-339">Особенности</span><span class="sxs-lookup"><span data-stu-id="486b1-339">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="486b1-340">Использование `UseDirectoryBrowser` и `UseStaticFiles` может привести к утечке конфиденциальной информации.</span><span class="sxs-lookup"><span data-stu-id="486b1-340">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="486b1-341">Настоятельно рекомендуется отключать просмотр каталогов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="486b1-341">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="486b1-342">Тщательно проверьте, просмотр каких каталогов разрешен посредством `UseStaticFiles` или `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="486b1-342">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="486b1-343">Весь каталог и его подкаталоги становятся общедоступными.</span><span class="sxs-lookup"><span data-stu-id="486b1-343">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="486b1-344">Храните файлы, предназначенные для общего доступа, в выделенных каталогах, таких как *\<content_root>/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="486b1-344">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="486b1-345">Отделите эти файлы от представлений MVC, страниц Razor Pages (только для версии 2.x), файлов конфигурации и т. д.</span><span class="sxs-lookup"><span data-stu-id="486b1-345">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="486b1-346">К URL-адресам содержимого, к которому предоставлен доступ методами `UseDirectoryBrowser` и `UseStaticFiles`, применяются те же требования по регистрозависимости и запрещенным символам, что и к базовой файловой системе.</span><span class="sxs-lookup"><span data-stu-id="486b1-346">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="486b1-347">Например, в Windows не учитывается регистр, а в macOS и Linux &mdash; учитывается.</span><span class="sxs-lookup"><span data-stu-id="486b1-347">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="486b1-348">Приложения ASP.NET Core, размещенные в IIS, используют [Модуль Core ASP.NET](xref:host-and-deploy/aspnet-core-module) для перенаправления всех запросов к приложению, включая запросы статических файлов.</span><span class="sxs-lookup"><span data-stu-id="486b1-348">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="486b1-349">Обработчик статических файлов IIS не используется.</span><span class="sxs-lookup"><span data-stu-id="486b1-349">The IIS static file handler isn't used.</span></span> <span data-ttu-id="486b1-350">Не существует возможности обработать запросы до того, как их обработает модуль.</span><span class="sxs-lookup"><span data-stu-id="486b1-350">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="486b1-351">Выполните следующие шаги в диспетчере служб IIS для удаления обработчика статических файлов IIS на уровне сервера или веб-сайта:</span><span class="sxs-lookup"><span data-stu-id="486b1-351">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="486b1-352">Перейдите к компоненту **Модули**.</span><span class="sxs-lookup"><span data-stu-id="486b1-352">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="486b1-353">Выберите в списке модуль **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="486b1-353">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="486b1-354">Нажмите кнопку **Удалить** в боковой панели **Действия**.</span><span class="sxs-lookup"><span data-stu-id="486b1-354">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="486b1-355">Если обработчик статических файлов IIS включен **и** модуль ASP.NET Core настроен неправильно, то статические файлы будут обслуживаться.</span><span class="sxs-lookup"><span data-stu-id="486b1-355">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="486b1-356">Это может случиться, если, например, не был развернут файл *web.config*.</span><span class="sxs-lookup"><span data-stu-id="486b1-356">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="486b1-357">Размещайте файлы с кодом (включая *CS* и *CSHTML*) за пределами [корневого каталога документов](xref:fundamentals/index#web-root) проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="486b1-357">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="486b1-358">Таким образом, в приложении создается логическое разделение между клиентским содержимым и серверным кодом.</span><span class="sxs-lookup"><span data-stu-id="486b1-358">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="486b1-359">Это предотвращает утечку серверного кода.</span><span class="sxs-lookup"><span data-stu-id="486b1-359">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="486b1-360">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="486b1-360">Additional resources</span></span>

* [<span data-ttu-id="486b1-361">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="486b1-361">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="486b1-362">Введение в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="486b1-362">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
