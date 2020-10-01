---
title: Статические файлы в ASP.NET Core
author: rick-anderson
description: Узнайте, как в веб-приложениях ASP.NET Core обслуживать и защищать статические файлы, а также как настраивать ПО промежуточного слоя по размещению статических файлов.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 36eeb3532ed5ea6f5f75c20cbe14331ed840a788
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393942"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="f9d3b-103">Статические файлы в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9d3b-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f9d3b-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="f9d3b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="f9d3b-105">Статические файлы, такие как HTML, CSS, изображения и JavaScript, являются ресурсами, которые приложения ASP.NET Core предоставляют клиентам напрямую по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="f9d3b-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9d3b-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="f9d3b-107">Обслуживание статических файлов</span><span class="sxs-lookup"><span data-stu-id="f9d3b-107">Serve static files</span></span>

<span data-ttu-id="f9d3b-108">Статические файлы хранятся в [корневом каталоге документов](xref:fundamentals/index#web-root) проекта.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="f9d3b-109">Каталог по умолчанию — `{content root}/wwwroot`, но его можно изменить с помощью метода <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="f9d3b-110">См. разделы [Корневой каталог содержимого](xref:fundamentals/index#content-root) и [Корневой веб-каталог](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="f9d3b-111">Метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> устанавливает текущий каталог в качестве корневого каталога содержимого:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="f9d3b-112">Предыдущий код создан с помощью шаблона веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="f9d3b-113">Статические файлы доступны по относительному пути от [корневого каталога документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="f9d3b-114">Например, шаблоны проекта **Web Application** содержат несколько папок в папке `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="f9d3b-115">Мы рекомендуем создать папку *wwwroot/images* и добавить в нее файл *wwwroot/images/MyImage.jpg*.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="f9d3b-116">Формат URI для доступа к файлу в папке `images`: `https://<hostname>/images/<image_file_name>`.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="f9d3b-117">Например: `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="f9d3b-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="f9d3b-118">Обслуживание файлов в корневом каталоге веб-сайта</span><span class="sxs-lookup"><span data-stu-id="f9d3b-118">Serve files in web root</span></span>

<span data-ttu-id="f9d3b-119">Шаблоны веб-приложений по умолчанию вызывают метод <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> в `Startup.Configure`, который позволяет обслуживать статические файлы:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="f9d3b-120">Эта перегрузка метода `UseStaticFiles` не принимает параметров, она помечает файлы в [корневом каталоге документов](xref:fundamentals/index#web-root) как обслуживаемые.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="f9d3b-121">Следующая разметка ссылается на *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="f9d3b-122">В приведенном выше коде знак тильды `~/` указывает на [корневой каталог документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="f9d3b-123">Обслуживание файлов вне корневого веб-каталога</span><span class="sxs-lookup"><span data-stu-id="f9d3b-123">Serve files outside of web root</span></span>

<span data-ttu-id="f9d3b-124">Пусть имеется иерархия каталогов, в которой статические файлы обслуживаются вне [корневого каталога документов](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="f9d3b-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="f9d3b-125">В запросе можно получить доступ к файлу `red-rose.jpg`, настроив ПО промежуточного слоя для статических файлов следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="f9d3b-126">В приведенном выше коде доступ к иерархии каталога *MyStaticFiles* представляется через сегмент URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="f9d3b-127">Запрос к `https://<hostname>/StaticFiles/images/red-rose.jpg` обслуживает файл *red-rose.jpg*.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="f9d3b-128">Следующая разметка ссылается на *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="f9d3b-129">Установка заголовков HTTP-ответов</span><span class="sxs-lookup"><span data-stu-id="f9d3b-129">Set HTTP response headers</span></span>

<span data-ttu-id="f9d3b-130">Для установки заголовков HTTP-ответов можно использовать объект <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="f9d3b-131">Кроме настройки обслуживания статических файлов в [корневом каталоге документов](xref:fundamentals/index#web-root), следующий код также устанавливает заголовок `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="f9d3b-132">Статические файлы являются общедоступными для кэширования в течение 600 с:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Добавлены заголовки ответов, включая заголовок Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="f9d3b-134">Авторизация статических файлов</span><span class="sxs-lookup"><span data-stu-id="f9d3b-134">Static file authorization</span></span>

<span data-ttu-id="f9d3b-135">ПО промежуточного слоя для статических файлов не предоставляет возможности авторизации.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="f9d3b-136">Все обслуживаемые им файлы, включая расположенные в `wwwroot`, находятся в открытом доступе.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="f9d3b-137">Для обслуживания файлов с авторизацией:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="f9d3b-138">Сохраните файлы за пределами каталога `wwwroot` в любом каталоге, к которому ПО промежуточного слоя для статических файлов имеет доступ по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-138">Store them outside of `wwwroot` and any directory accessible to the default Static File Middleware.</span></span>
* <span data-ttu-id="f9d3b-139">Вызовите `UseStaticFiles` после `UseAuthorization` и укажите путь.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-139">Call `UseStaticFiles` after `UseAuthorization` and specify the path:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  <span data-ttu-id="f9d3b-140">Предыдущий подход требует, чтобы пользователи прошли проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-140">The preceding approach requires users to be authenticated:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="f9d3b-141">Альтернативный подход к обработке файлов на основе авторизации</span><span class="sxs-lookup"><span data-stu-id="f9d3b-141">An alternative approach to serve files based on authorization:</span></span>

* <span data-ttu-id="f9d3b-142">Сохраните файлы за пределами каталога `wwwroot` в любом каталоге, к которому имеет доступ ПО промежуточного слоя для статических файлов.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-142">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="f9d3b-143">Обслуживайте их через метод действия, к которому применима авторизация, и получите объект <xref:Microsoft.AspNetCore.Mvc.FileResult>:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-143">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="f9d3b-144">Просмотр каталогов</span><span class="sxs-lookup"><span data-stu-id="f9d3b-144">Directory browsing</span></span>

<span data-ttu-id="f9d3b-145">Просмотр каталогов позволяет просматривать список каталогов в указанных каталогах.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-145">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="f9d3b-146">По соображениям безопасности просмотр каталогов отключен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-146">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="f9d3b-147">См. раздел [Безопасность статических файлов](#considerations).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-147">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="f9d3b-148">Включите просмотр каталогов с помощью:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-148">Enable directory browsing with:</span></span>

* <span data-ttu-id="f9d3b-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> в `Startup.ConfigureServices`;</span><span class="sxs-lookup"><span data-stu-id="f9d3b-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="f9d3b-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> в `Startup.Configure`;</span><span class="sxs-lookup"><span data-stu-id="f9d3b-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="f9d3b-151">Приведенный выше код разрешает просмотр папки *wwwroot/images* с помощью URL-адреса `https://<hostname>/MyImages`, со ссылками на все файлы и папки:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-151">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![просмотр каталогов](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="f9d3b-153">Обслуживание документов по умолчанию</span><span class="sxs-lookup"><span data-stu-id="f9d3b-153">Serve default documents</span></span>

<span data-ttu-id="f9d3b-154">Страница по умолчанию является для пользователей отправной точкой на веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-154">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="f9d3b-155">Чтобы обслуживать страницу по умолчанию из `wwwroot` без полного URI, вызовите метод <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A>:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-155">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="f9d3b-156">Для обслуживания файла по умолчанию метод `UseDefaultFiles`должен быть вызван до метода `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-156">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="f9d3b-157">`UseDefaultFiles` — это средство переопределения URL-адресов, которое не обслуживает файл.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-157">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="f9d3b-158">При использовании `UseDefaultFiles` запросы к папке в `wwwroot` будут искать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-158">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="f9d3b-159">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-159">*default.htm*</span></span>
* <span data-ttu-id="f9d3b-160">*default.html*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-160">*default.html*</span></span>
* <span data-ttu-id="f9d3b-161">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-161">*index.htm*</span></span>
* <span data-ttu-id="f9d3b-162">*index.html*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-162">*index.html*</span></span>

<span data-ttu-id="f9d3b-163">Первый найденный файл из списка будет обслужен, как будто был введен полный URI.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-163">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="f9d3b-164">URL-адрес в браузере будет соответствовать запрошенному URI.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-164">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="f9d3b-165">Следующий код позволяет изменить имя файла по умолчанию на *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-165">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="f9d3b-166">В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-166">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="f9d3b-167">Использование UseFileServer для документов по умолчанию</span><span class="sxs-lookup"><span data-stu-id="f9d3b-167">UseFileServer for default documents</span></span>

<span data-ttu-id="f9d3b-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> объединяет функции `UseStaticFiles`, `UseDefaultFiles` и при необходимости `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="f9d3b-169">Вызовите `app.UseFileServer`, чтобы включить обслуживание статических файлов и файла по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-169">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="f9d3b-170">Просмотр каталогов отключен.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-170">Directory browsing isn't enabled.</span></span> <span data-ttu-id="f9d3b-171">В следующем примере кода демонстрируется `Startup.Configure` с `UseFileServer`:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-171">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="f9d3b-172">Следующий пример кода позволяет обслуживать статические файлы, файл по умолчанию и просмотр каталогов:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-172">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="f9d3b-173">В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-173">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="f9d3b-174">Пусть имеется следующая иерархия каталогов:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-174">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="f9d3b-175">Следующий пример кода позволяет обслуживать статические файлы, файл по умолчанию и просмотр каталогов `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-175">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="f9d3b-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> должно вызываться, когда свойство `EnableDirectoryBrowsing` имеет значение `true`.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="f9d3b-177">При указанных выше коде и иерархии файлов URL-адреса будут разрешаться следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-177">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="f9d3b-178">URI</span><span class="sxs-lookup"><span data-stu-id="f9d3b-178">URI</span></span>            |      <span data-ttu-id="f9d3b-179">Ответ</span><span class="sxs-lookup"><span data-stu-id="f9d3b-179">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="f9d3b-180">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-180">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="f9d3b-181">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-181">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="f9d3b-182">Если в каталоге *MyStaticFiles* отсутствует файл с именем по умолчанию, `https://<hostname>/StaticFiles` возвращает список содержимого каталога с доступными для перехода ссылками:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-182">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Список статических файлов](static-files/_static/db2.png)

<span data-ttu-id="f9d3b-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> и <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> выполняют перенаправление на стороне клиента из целевого URI без `/` в конце в целевой URI с `/` в конце.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="f9d3b-185">Например, из `https://<hostname>/StaticFiles` в `https://<hostname>/StaticFiles/`.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-185">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="f9d3b-186">Относительные URL-адреса в каталоге *StaticFiles* считаются недопустимыми без косой черты в конце (`/`).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-186">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="f9d3b-187">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="f9d3b-187">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="f9d3b-188">Класс <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> содержит свойство `Mappings`, которое используется для сопоставления расширений файлов и типов содержимого MIME.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-188">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="f9d3b-189">В следующем примере несколько расширений файлов сопоставляются с известными типами MIME.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-189">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="f9d3b-190">Расширение *.rtf* заменяется, а *.mp4* удаляется:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-190">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="f9d3b-191">В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-191">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="f9d3b-192">См. раздел [Типы содержимого MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-192">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="f9d3b-193">Нестандартные типы содержимого</span><span class="sxs-lookup"><span data-stu-id="f9d3b-193">Non-standard content types</span></span>

<span data-ttu-id="f9d3b-194">ПО промежуточного слоя для статических файлов распознает почти 400 известных типов содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-194">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="f9d3b-195">Если пользователь запрашивает файл неизвестного типа, ПО промежуточного слоя статических файлов передает запрос следующему компоненту ПО промежуточного слоя в конвейере.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-195">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="f9d3b-196">Если ПО промежуточного слоя не удается обработать запрос, возвращается ответ *404 Не найдено*.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-196">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="f9d3b-197">Если просмотр каталогов разрешен, то в списке каталогов отображается ссылка на файл.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-197">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="f9d3b-198">Следующий код включает обслуживание неизвестных типов и обслуживает неизвестные файлы как изображения:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-198">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="f9d3b-199">В следующем коде демонстрируется `Startup.Configure` с приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-199">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="f9d3b-200">При выполнении вышеописанного кода ответ на запрос файла с неизвестным типом содержимого вернется в виде изображения.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-200">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="f9d3b-201">При включении <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> возникает угроза безопасности.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-201">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="f9d3b-202">По умолчанию он отключен, и его использование не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-202">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="f9d3b-203">Использование класса [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) является более безопасной альтернативой для обслуживания файлов с нестандартными расширениями.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="f9d3b-204">Предоставление файлов из нескольких расположений</span><span class="sxs-lookup"><span data-stu-id="f9d3b-204">Serve files from multiple locations</span></span>

<span data-ttu-id="f9d3b-205">В качестве значений `UseStaticFiles` и `UseFileServer` по умолчанию используется поставщик файлов, указывающий на `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-205">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="f9d3b-206">Вы можете предоставить дополнительные экземпляры `UseStaticFiles` и `UseFileServer` с другими поставщиками файлов для обслуживания файлов из других расположений.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-206">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="f9d3b-207">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-207">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="f9d3b-208">Безопасность статических файлов</span><span class="sxs-lookup"><span data-stu-id="f9d3b-208">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="f9d3b-209">Использование `UseDirectoryBrowser` и `UseStaticFiles` может привести к утечке конфиденциальной информации.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-209">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="f9d3b-210">Настоятельно рекомендуется отключать просмотр каталогов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-210">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="f9d3b-211">Тщательно проверьте, просмотр каких каталогов разрешен посредством `UseStaticFiles` или `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-211">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="f9d3b-212">Весь каталог и его подкаталоги становятся общедоступными.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-212">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="f9d3b-213">Храните файлы, предназначенные для общего доступа, в выделенных каталогах, таких как `<content_root>/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-213">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="f9d3b-214">Отделите эти файлы от представлений MVC, Razor Pages, файлов конфигурации и т. д.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-214">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="f9d3b-215">К URL-адресам содержимого, к которому предоставлен доступ методами `UseDirectoryBrowser` и `UseStaticFiles`, применяются те же требования по регистрозависимости и запрещенным символам, что и к базовой файловой системе.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-215">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="f9d3b-216">Например, в Windows регистр не учитывается, а в macOS и Linux учитывается.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-216">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="f9d3b-217">Приложения ASP.NET Core, размещенные в IIS, используют [Модуль Core ASP.NET](xref:host-and-deploy/aspnet-core-module) для перенаправления всех запросов к приложению, включая запросы статических файлов.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-217">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="f9d3b-218">Обработчик статических файлов IIS не используется и не выполняет обработку запросов.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-218">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="f9d3b-219">Выполните следующие шаги в диспетчере служб IIS для удаления обработчика статических файлов IIS на уровне сервера или веб-сайта:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-219">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="f9d3b-220">Перейдите к компоненту **Модули**.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-220">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="f9d3b-221">Выберите в списке модуль **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-221">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="f9d3b-222">Нажмите кнопку **Удалить** в боковой панели **Действия**.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-222">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="f9d3b-223">Если обработчик статических файлов IIS включен **и** модуль ASP.NET Core настроен неправильно, то статические файлы будут обслуживаться.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-223">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="f9d3b-224">Это может случиться, если, например, не был развернут файл *web.config*.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-224">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="f9d3b-225">Размещайте файлы с кодом, включая файлы *.cs* и *.cshtml*, за пределами [корневого веб-каталога](xref:fundamentals/index#web-root) проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-225">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="f9d3b-226">Таким образом, в приложении создается логическое разделение между клиентским содержимым и серверным кодом.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-226">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="f9d3b-227">Это предотвращает утечку серверного кода.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-227">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9d3b-228">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f9d3b-228">Additional resources</span></span>

* [<span data-ttu-id="f9d3b-229">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="f9d3b-229">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="f9d3b-230">Введение в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9d3b-230">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f9d3b-231">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="f9d3b-231">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="f9d3b-232">Статические файлы, такие как HTML, CSS, изображения и JavaScript, являются ресурсами, которые приложения ASP.NET Core предоставляют клиентам напрямую.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-232">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="f9d3b-233">Для обслуживания таких файлов требуется настроить некоторые параметры.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-233">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="f9d3b-234">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9d3b-234">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="f9d3b-235">Обслуживание статических файлов</span><span class="sxs-lookup"><span data-stu-id="f9d3b-235">Serve static files</span></span>

<span data-ttu-id="f9d3b-236">Статические файлы хранятся в [корневом каталоге документов](xref:fundamentals/index#web-root) проекта.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-236">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="f9d3b-237">Каталог по умолчанию — *{корневой_каталог_содержимого}/wwwroot*, но его можно изменить с помощью метода <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-237">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="f9d3b-238">Дополнительные сведения см. в разделах [Корневой каталог содержимого](xref:fundamentals/index#content-root) и [Корневой веб-каталог](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-238">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="f9d3b-239">Веб-узел приложения должен знать о расположении корневого каталога содержимого.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-239">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="f9d3b-240">Метод `WebHost.CreateDefaultBuilder` устанавливает текущий каталог в качестве корневого каталога содержимого:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-240">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="f9d3b-241">Статические файлы доступны по относительному пути от [корневого каталога документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-241">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="f9d3b-242">Например, шаблон проекта **Web Application** содержит несколько папок в папке `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-242">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="f9d3b-243">Формат URI для доступа к файлу во вложенной папке *images*: *http://\<server_address>/images/\<image_file_name>* .</span><span class="sxs-lookup"><span data-stu-id="f9d3b-243">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="f9d3b-244">Например, *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="f9d3b-244">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="f9d3b-245">Если код предназначен для .NET Framework, добавьте в проект пакет [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-245">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="f9d3b-246">Если код предназначен для .NET Core, то этот пакет уже включен в метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-246">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="f9d3b-247">Настройте [ПО промежуточного слоя](xref:fundamentals/middleware/index), позволяющее обслуживать статические файлы.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-247">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="f9d3b-248">Обслуживание файлов в корневом веб-каталоге</span><span class="sxs-lookup"><span data-stu-id="f9d3b-248">Serve files inside of web root</span></span>

<span data-ttu-id="f9d3b-249">Вызовите метод <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-249">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="f9d3b-250">Эта перегрузка метода `UseStaticFiles` не принимает параметров, она помечает файлы в [корневом каталоге документов](xref:fundamentals/index#web-root) как обслуживаемые.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-250">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="f9d3b-251">Следующая разметка ссылается на *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-251">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="f9d3b-252">В приведенном выше коде знак тильды `~/` указывает на [корневой каталог документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-252">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="f9d3b-253">Обслуживание файлов вне корневого веб-каталога</span><span class="sxs-lookup"><span data-stu-id="f9d3b-253">Serve files outside of web root</span></span>

<span data-ttu-id="f9d3b-254">Пусть имеется иерархия каталогов, в которой статические файлы обслуживаются вне [корневого каталога документов](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="f9d3b-254">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="f9d3b-255">В запросе можно получить доступ к файлу *banner1.svg*, настроив ПО промежуточного слоя для статических файлов следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-255">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="f9d3b-256">В приведенном выше коде доступ к иерархии каталога *MyStaticFiles* представляется через сегмент URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-256">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="f9d3b-257">Запрос *http://\<server_address>/StaticFiles/images/banner1.svg* обслуживает файл *banner1.svg*.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-257">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="f9d3b-258">Следующая разметка ссылается на *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-258">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="f9d3b-259">Установка заголовков HTTP-ответов</span><span class="sxs-lookup"><span data-stu-id="f9d3b-259">Set HTTP response headers</span></span>

<span data-ttu-id="f9d3b-260">Для установки заголовков HTTP-ответов можно использовать объект <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-260">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="f9d3b-261">Кроме настройки обслуживания статических файлов в [корневом каталоге документов](xref:fundamentals/index#web-root), следующий код также устанавливает заголовок `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-261">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f9d3b-262">Метод <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> существует в пакете [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-262">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="f9d3b-263">Файлы, к которым был предоставлен доступ, кэшируются в течение 10 минут (600 секунд) в среде разработки:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-263">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Добавлены заголовки ответов, включая заголовок Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="f9d3b-265">Авторизация статических файлов</span><span class="sxs-lookup"><span data-stu-id="f9d3b-265">Static file authorization</span></span>

<span data-ttu-id="f9d3b-266">ПО промежуточного слоя для статических файлов не предоставляет возможности авторизации.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-266">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="f9d3b-267">Все обслуживаемые им файлы, включая расположенные в *wwwroot*, находятся в открытом доступе.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-267">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="f9d3b-268">Для обслуживания файлов с авторизацией:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-268">To serve files based on authorization:</span></span>

* <span data-ttu-id="f9d3b-269">Сохраните файлы в любом каталоге за пределами каталога *wwwroot*, к которому имеет доступ ПО промежуточного слоя для статических файлов.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-269">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="f9d3b-270">Обслуживайте их через метод действия, к которому применима авторизация.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-270">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="f9d3b-271">Получите объект <xref:Microsoft.AspNetCore.Mvc.FileResult>:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-271">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="f9d3b-272">Просмотр каталогов</span><span class="sxs-lookup"><span data-stu-id="f9d3b-272">Enable directory browsing</span></span>

<span data-ttu-id="f9d3b-273">Просмотр каталогов позволяет пользователям веб-приложениям просматривать файлы и каталоги внутри определенного каталога.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-273">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="f9d3b-274">По соображениям безопасности просмотр каталогов отключен по умолчанию (см. раздел [Особенности](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-274">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="f9d3b-275">Включите просмотр каталогов, вызвав метод <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-275">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="f9d3b-276">Добавьте необходимые службы путем вызова метода <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> из `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-276">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="f9d3b-277">Приведенный выше код разрешает просмотр папки *wwwroot/images* с помощью URL-адреса *http://\<server_address>/MyImages*, со ссылками на все файлы и папки:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-277">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![просмотр каталогов](static-files/_static/dir-browse.png)

<span data-ttu-id="f9d3b-279">Информацию об угрозе безопасности при включении просмотра каталогов см. в разделе [Особенности](#considerations).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-279">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="f9d3b-280">Обратите внимание на два вызова метода `UseStaticFiles` в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-280">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="f9d3b-281">Первый вызов включает обслуживание статических файлов в папке *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-281">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="f9d3b-282">Второй вызов включает просмотр папки *wwwroot/images* с помощью URL-адреса *http://\<server_address>/MyImages*:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-282">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="f9d3b-283">Обслуживание документа по умолчанию</span><span class="sxs-lookup"><span data-stu-id="f9d3b-283">Serve a default document</span></span>

<span data-ttu-id="f9d3b-284">Домашняя страница по умолчанию является для пользователей логической отправной точкой при посещении веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-284">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="f9d3b-285">Для обслуживания страницы по умолчанию без указания полного имени URI вызовите метод <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> из `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-285">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="f9d3b-286">Для обслуживания файла по умолчанию метод `UseDefaultFiles`должен быть вызван до метода `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-286">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="f9d3b-287">Метод `UseDefaultFiles` фактически не обслуживает файл, а только перезаписывает URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-287">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="f9d3b-288">Для обслуживания файла включите ПО промежуточного слоя для статических файлов, вызвав метод `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-288">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="f9d3b-289">При вызове метода `UseDefaultFiles` запросы к папке будут искать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-289">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="f9d3b-290">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-290">*default.htm*</span></span>
* <span data-ttu-id="f9d3b-291">*default.html*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-291">*default.html*</span></span>
* <span data-ttu-id="f9d3b-292">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-292">*index.htm*</span></span>
* <span data-ttu-id="f9d3b-293">*index.html*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-293">*index.html*</span></span>

<span data-ttu-id="f9d3b-294">Первый найденный файл из списка будет обслужен, как будто был введен полный URI.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-294">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="f9d3b-295">URL-адрес в браузере будет соответствовать запрошенному URI.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-295">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="f9d3b-296">Следующий код позволяет изменить имя файла по умолчанию на *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-296">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="f9d3b-297">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="f9d3b-297">UseFileServer</span></span>

<span data-ttu-id="f9d3b-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> объединяет функции `UseStaticFiles`, `UseDefaultFiles` и при необходимости `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="f9d3b-299">Следующий пример кода позволяет обслуживать статические файлы и файл по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-299">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="f9d3b-300">Просмотр каталогов отключен.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-300">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="f9d3b-301">Следующий код отличается от предыдущей перегрузки метода без параметров включением просмотра каталогов:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-301">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="f9d3b-302">Пусть имеется следующая иерархия каталогов:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-302">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="f9d3b-303">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="f9d3b-303">**wwwroot**</span></span>
  * <span data-ttu-id="f9d3b-304">**css**</span><span class="sxs-lookup"><span data-stu-id="f9d3b-304">**css**</span></span>
  * <span data-ttu-id="f9d3b-305">**images**</span><span class="sxs-lookup"><span data-stu-id="f9d3b-305">**images**</span></span>
  * <span data-ttu-id="f9d3b-306">**js**</span><span class="sxs-lookup"><span data-stu-id="f9d3b-306">**js**</span></span>
* <span data-ttu-id="f9d3b-307">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="f9d3b-307">**MyStaticFiles**</span></span>
  * <span data-ttu-id="f9d3b-308">**images**</span><span class="sxs-lookup"><span data-stu-id="f9d3b-308">**images**</span></span>
    * <span data-ttu-id="f9d3b-309">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-309">*banner1.svg*</span></span>
  * <span data-ttu-id="f9d3b-310">*default.html*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-310">*default.html*</span></span>

<span data-ttu-id="f9d3b-311">Следующий пример кода включает обслуживание статических файлов, файлы по умолчанию и просмотр каталога `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-311">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="f9d3b-312">Метод `AddDirectoryBrowser` должен вызываться при значении `true` свойства `EnableDirectoryBrowsing`:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-312">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="f9d3b-313">При указанных выше коде и иерархии файлов URL-адреса будут разрешаться следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-313">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="f9d3b-314">URI</span><span class="sxs-lookup"><span data-stu-id="f9d3b-314">URI</span></span>            |                             <span data-ttu-id="f9d3b-315">Ответ</span><span class="sxs-lookup"><span data-stu-id="f9d3b-315">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="f9d3b-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="f9d3b-317">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="f9d3b-317">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="f9d3b-318">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="f9d3b-318">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="f9d3b-319">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="f9d3b-319">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="f9d3b-320">Если в каталоге *MyStaticFiles* отсутствует файл с именем по умолчанию, *http://\<server_address>/StaticFiles* возвращает список содержимого каталога с доступными для перехода ссылками:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-320">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Список статических файлов](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="f9d3b-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> и <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> выполняют перенаправление на стороне клиента из `http://{SERVER ADDRESS}/StaticFiles` (без косой черты в конце) в `http://{SERVER ADDRESS}/StaticFiles/` (с косой чертой в конце).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="f9d3b-323">Относительные URL-адреса в каталоге *StaticFiles* считаются недопустимыми без косой черты в конце.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-323">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="f9d3b-324">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="f9d3b-324">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="f9d3b-325">Класс <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> содержит свойство `Mappings`, которое используется для сопоставления расширений файлов и типов содержимого MIME.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-325">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="f9d3b-326">В следующем примере несколько расширений файлов регистрируются в известные типы MIME.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-326">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="f9d3b-327">Расширение *.rtf* заменяется, а *.mp4* удаляется.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-327">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="f9d3b-328">См. раздел [Типы содержимого MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-328">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="f9d3b-329">Сведения об использовании настраиваемых <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> и настройке других <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> в приложениях Blazor Server см. в разделе <xref:blazor/fundamentals/additional-scenarios#static-files>.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-329">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/additional-scenarios#static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="f9d3b-330">Нестандартные типы содержимого</span><span class="sxs-lookup"><span data-stu-id="f9d3b-330">Non-standard content types</span></span>

<span data-ttu-id="f9d3b-331">ПО промежуточного слоя для статических файлов воспринимает почти 400 известных типов содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-331">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="f9d3b-332">Если пользователь запрашивает файл неизвестного типа, ПО промежуточного слоя статических файлов передает запрос следующему компоненту ПО промежуточного слоя в конвейере.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-332">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="f9d3b-333">Если ПО промежуточного слоя не удается обработать запрос, возвращается ответ *404 Не найдено*.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-333">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="f9d3b-334">Если просмотр каталогов разрешен, то в списке каталогов отображается ссылка на файл.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-334">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="f9d3b-335">Следующий код включает обслуживание неизвестных типов и обслуживает неизвестные файлы как изображения:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-335">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="f9d3b-336">При выполнении вышеописанного кода ответ на запрос файла с неизвестным типом содержимого вернется в виде изображения.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-336">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="f9d3b-337">При включении <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> возникает угроза безопасности.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-337">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="f9d3b-338">По умолчанию он отключен, и его использование не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-338">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="f9d3b-339">Использование класса [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) является более безопасной альтернативой для обслуживания файлов с нестандартными расширениями.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="f9d3b-340">Предоставление файлов из нескольких расположений</span><span class="sxs-lookup"><span data-stu-id="f9d3b-340">Serve files from multiple locations</span></span>

<span data-ttu-id="f9d3b-341">В качестве значений `UseStaticFiles` и `UseFileServer` по умолчанию используется поставщик файлов, указывающий на *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-341">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="f9d3b-342">Вы можете предоставить дополнительные экземпляры `UseStaticFiles` и `UseFileServer` с другими поставщиками файлов для предоставления файлов из других расположений.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-342">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="f9d3b-343">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="f9d3b-343">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="f9d3b-344">Особенности</span><span class="sxs-lookup"><span data-stu-id="f9d3b-344">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="f9d3b-345">Использование `UseDirectoryBrowser` и `UseStaticFiles` может привести к утечке конфиденциальной информации.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-345">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="f9d3b-346">Настоятельно рекомендуется отключать просмотр каталогов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-346">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="f9d3b-347">Тщательно проверьте, просмотр каких каталогов разрешен посредством `UseStaticFiles` или `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-347">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="f9d3b-348">Весь каталог и его подкаталоги становятся общедоступными.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-348">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="f9d3b-349">Храните файлы, предназначенные для общего доступа, в выделенных каталогах, таких как *\<content_root>/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-349">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="f9d3b-350">Отделите эти файлы от представлений MVC, страниц Razor Pages (только для версии 2.x), файлов конфигурации и т. д.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-350">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="f9d3b-351">К URL-адресам содержимого, к которому предоставлен доступ методами `UseDirectoryBrowser` и `UseStaticFiles`, применяются те же требования по регистрозависимости и запрещенным символам, что и к базовой файловой системе.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-351">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="f9d3b-352">Например, в Windows не учитывается регистр, а в macOS и Linux &mdash; учитывается.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-352">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="f9d3b-353">Приложения ASP.NET Core, размещенные в IIS, используют [Модуль Core ASP.NET](xref:host-and-deploy/aspnet-core-module) для перенаправления всех запросов к приложению, включая запросы статических файлов.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-353">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="f9d3b-354">Обработчик статических файлов IIS не используется.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-354">The IIS static file handler isn't used.</span></span> <span data-ttu-id="f9d3b-355">Не существует возможности обработать запросы до того, как их обработает модуль.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-355">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="f9d3b-356">Выполните следующие шаги в диспетчере служб IIS для удаления обработчика статических файлов IIS на уровне сервера или веб-сайта:</span><span class="sxs-lookup"><span data-stu-id="f9d3b-356">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="f9d3b-357">Перейдите к компоненту **Модули**.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-357">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="f9d3b-358">Выберите в списке модуль **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-358">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="f9d3b-359">Нажмите кнопку **Удалить** в боковой панели **Действия**.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-359">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="f9d3b-360">Если обработчик статических файлов IIS включен **и** модуль ASP.NET Core настроен неправильно, то статические файлы будут обслуживаться.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-360">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="f9d3b-361">Это может случиться, если, например, не был развернут файл *web.config*.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-361">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="f9d3b-362">Размещайте файлы с кодом (включая *CS* и *CSHTML*) за пределами [корневого каталога документов](xref:fundamentals/index#web-root) проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-362">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="f9d3b-363">Таким образом, в приложении создается логическое разделение между клиентским содержимым и серверным кодом.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-363">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="f9d3b-364">Это предотвращает утечку серверного кода.</span><span class="sxs-lookup"><span data-stu-id="f9d3b-364">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9d3b-365">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f9d3b-365">Additional resources</span></span>

* [<span data-ttu-id="f9d3b-366">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="f9d3b-366">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="f9d3b-367">Введение в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9d3b-367">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
