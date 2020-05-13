---
title: Статические файлы в ASP.NET Core
author: rick-anderson
description: Узнайте, как в веб-приложениях ASP.NET Core обслуживать и защищать статические файлы, а также как настраивать ПО промежуточного слоя по размещению статических файлов.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: f7697260e6ab29d0e9ba955dfdf0c8c81e4e1130
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775496"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="49945-103">Статические файлы в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49945-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="49945-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="49945-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="49945-105">Статические файлы, такие как HTML, CSS, изображения и JavaScript, являются ресурсами, которые приложения ASP.NET Core предоставляют клиентам напрямую.</span><span class="sxs-lookup"><span data-stu-id="49945-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="49945-106">Для обслуживания таких файлов требуется настроить некоторые параметры.</span><span class="sxs-lookup"><span data-stu-id="49945-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="49945-107">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49945-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="49945-108">Обслуживание статических файлов</span><span class="sxs-lookup"><span data-stu-id="49945-108">Serve static files</span></span>

<span data-ttu-id="49945-109">Статические файлы хранятся в [корневом каталоге документов](xref:fundamentals/index#web-root) проекта.</span><span class="sxs-lookup"><span data-stu-id="49945-109">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="49945-110">Каталог по умолчанию — *{корневой_каталог_содержимого}/wwwroot*, но его можно изменить через метод [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_).</span><span class="sxs-lookup"><span data-stu-id="49945-110">The default directory is *{content root}/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="49945-111">Дополнительные сведения см. в разделах [Корневой каталог содержимого](xref:fundamentals/index#content-root) и [Корневой веб-каталог](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="49945-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="49945-112">Веб-узел приложения должен знать о расположении корневого каталога содержимого.</span><span class="sxs-lookup"><span data-stu-id="49945-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="49945-113">Метод `WebHost.CreateDefaultBuilder` устанавливает текущий каталог в качестве корневого каталога содержимого:</span><span class="sxs-lookup"><span data-stu-id="49945-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="49945-114">Установите текущий каталог в качестве корневого каталога содержимого, вызвав [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) в методе `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="49945-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="49945-115">Статические файлы доступны по относительному пути от [корневого каталога документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="49945-115">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="49945-116">Например, шаблон проекта **Web Application** содержит несколько папок в папке *wwwroot*:</span><span class="sxs-lookup"><span data-stu-id="49945-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="49945-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="49945-117">**wwwroot**</span></span>
  * <span data-ttu-id="49945-118">**css**</span><span class="sxs-lookup"><span data-stu-id="49945-118">**css**</span></span>
  * <span data-ttu-id="49945-119">**images**</span><span class="sxs-lookup"><span data-stu-id="49945-119">**images**</span></span>
  * <span data-ttu-id="49945-120">**js**</span><span class="sxs-lookup"><span data-stu-id="49945-120">**js**</span></span>

<span data-ttu-id="49945-121">Формат URI для доступа к файлу во вложенной папке *images* следующий: *http://\<адрес_сервера>/images/\<имя_файла_изображения>* .</span><span class="sxs-lookup"><span data-stu-id="49945-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="49945-122">Например, *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="49945-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="49945-123">Если код предназначен для .NET Framework, добавьте в проект пакет [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/).</span><span class="sxs-lookup"><span data-stu-id="49945-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="49945-124">Если код предназначен для .NET Core, то этот пакет уже включен в метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="49945-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="49945-125">Если код предназначен для .NET Framework, добавьте в проект пакет [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/).</span><span class="sxs-lookup"><span data-stu-id="49945-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="49945-126">Если код предназначен для .NET Core, то этот пакет уже включен в метапакет [Microsoft.AspNetCore.All](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="49945-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="49945-127">Добавьте пакет [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) в проект.</span><span class="sxs-lookup"><span data-stu-id="49945-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span>

::: moniker-end

<span data-ttu-id="49945-128">Настройте [ПО промежуточного слоя](xref:fundamentals/middleware/index), позволяющее обслуживать статические файлы.</span><span class="sxs-lookup"><span data-stu-id="49945-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="49945-129">Обслуживание файлов в корневом веб-каталоге</span><span class="sxs-lookup"><span data-stu-id="49945-129">Serve files inside of web root</span></span>

<span data-ttu-id="49945-130">Вызовите метод [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="49945-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="49945-131">Эта перегрузка метода `UseStaticFiles` не принимает параметров, она помечает файлы в [корневом каталоге документов](xref:fundamentals/index#web-root) как обслуживаемые.</span><span class="sxs-lookup"><span data-stu-id="49945-131">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="49945-132">Следующая разметка ссылается на *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="49945-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="49945-133">В приведенном выше коде знак тильды `~/` указывает на [корневой каталог документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="49945-133">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="49945-134">Обслуживание файлов вне корневого веб-каталога</span><span class="sxs-lookup"><span data-stu-id="49945-134">Serve files outside of web root</span></span>

<span data-ttu-id="49945-135">Пусть имеется иерархия каталогов, в которой статические файлы обслуживаются вне [корневого каталога документов](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="49945-135">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* <span data-ttu-id="49945-136">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="49945-136">**wwwroot**</span></span>
  * <span data-ttu-id="49945-137">**css**</span><span class="sxs-lookup"><span data-stu-id="49945-137">**css**</span></span>
  * <span data-ttu-id="49945-138">**images**</span><span class="sxs-lookup"><span data-stu-id="49945-138">**images**</span></span>
  * <span data-ttu-id="49945-139">**js**</span><span class="sxs-lookup"><span data-stu-id="49945-139">**js**</span></span>
* <span data-ttu-id="49945-140">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="49945-140">**MyStaticFiles**</span></span>
  * <span data-ttu-id="49945-141">**images**</span><span class="sxs-lookup"><span data-stu-id="49945-141">**images**</span></span>
    * <span data-ttu-id="49945-142">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="49945-142">*banner1.svg*</span></span>

<span data-ttu-id="49945-143">В запросе можно получить доступ к файлу *banner1.svg*, настроив ПО промежуточного слоя для статических файлов следующим образом:</span><span class="sxs-lookup"><span data-stu-id="49945-143">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="49945-144">В приведенном выше коде доступ к иерархии каталога *MyStaticFiles* представляется через сегмент URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="49945-144">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="49945-145">Запрос *http://\<адрес_сервера> /StaticFiles/images/banner1.svg* обслуживает файлы *banner1.svg*.</span><span class="sxs-lookup"><span data-stu-id="49945-145">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="49945-146">Следующая разметка ссылается на *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="49945-146">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="49945-147">Установка заголовков HTTP-ответов</span><span class="sxs-lookup"><span data-stu-id="49945-147">Set HTTP response headers</span></span>

<span data-ttu-id="49945-148">Для установки заголовков HTTP-ответов можно использовать объект [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions).</span><span class="sxs-lookup"><span data-stu-id="49945-148">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="49945-149">Кроме настройки обслуживания статических файлов в [корневом каталоге документов](xref:fundamentals/index#web-root), следующий код также устанавливает заголовок `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="49945-149">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="49945-150">Метод [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) содержится в пакете [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="49945-150">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="49945-151">Файлы, к которым был предоставлен доступ, кэшируются в течение 10 минут (600 секунд) в среде разработки:</span><span class="sxs-lookup"><span data-stu-id="49945-151">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Добавлены заголовки ответов, включая заголовок Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="49945-153">Авторизация статических файлов</span><span class="sxs-lookup"><span data-stu-id="49945-153">Static file authorization</span></span>

<span data-ttu-id="49945-154">ПО промежуточного слоя для статических файлов не предоставляет возможности авторизации.</span><span class="sxs-lookup"><span data-stu-id="49945-154">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="49945-155">Все обслуживаемые им файлы, включая расположенные в *wwwroot*, находятся в открытом доступе.</span><span class="sxs-lookup"><span data-stu-id="49945-155">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="49945-156">Для обслуживания файлов с авторизацией:</span><span class="sxs-lookup"><span data-stu-id="49945-156">To serve files based on authorization:</span></span>

* <span data-ttu-id="49945-157">Сохраните файлы в любом каталоге за пределами каталога *wwwroot*, к которому имеет доступ ПО промежуточного слоя для статических файлов.</span><span class="sxs-lookup"><span data-stu-id="49945-157">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="49945-158">Обслуживайте их через метод действия, к которому применима авторизация.</span><span class="sxs-lookup"><span data-stu-id="49945-158">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="49945-159">Возвращайте объект [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult):</span><span class="sxs-lookup"><span data-stu-id="49945-159">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="49945-160">Просмотр каталогов</span><span class="sxs-lookup"><span data-stu-id="49945-160">Enable directory browsing</span></span>

<span data-ttu-id="49945-161">Просмотр каталогов позволяет пользователям веб-приложениям просматривать файлы и каталоги внутри определенного каталога.</span><span class="sxs-lookup"><span data-stu-id="49945-161">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="49945-162">По соображениям безопасности просмотр каталогов отключен по умолчанию (см. раздел [Особенности](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="49945-162">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="49945-163">Разрешить просмотр каталогов можно вызвав метод [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="49945-163">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="49945-164">Добавим необходимые службы путем вызова метода [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) из `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="49945-164">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="49945-165">Приведенный выше код разрешает просмотр папки *wwwroot/images* с помощью URL-адреса *http://\<адрес_сервера>/MyImages*, со ссылками на все файлы и папки:</span><span class="sxs-lookup"><span data-stu-id="49945-165">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![просмотр каталогов](static-files/_static/dir-browse.png)

<span data-ttu-id="49945-167">Информацию об угрозе безопасности при включении просмотра каталогов см. в разделе [Особенности](#considerations).</span><span class="sxs-lookup"><span data-stu-id="49945-167">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="49945-168">Обратите внимание на два вызова метода `UseStaticFiles` в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="49945-168">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="49945-169">Первый вызов включает обслуживание статических файлов в папке *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="49945-169">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="49945-170">Второй вызов включает просмотр каталогов в папке *wwwroot/images* с помощью URL-адреса *http://\<адрес_сервера>/MyImages*:</span><span class="sxs-lookup"><span data-stu-id="49945-170">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="49945-171">Обслуживание документа по умолчанию</span><span class="sxs-lookup"><span data-stu-id="49945-171">Serve a default document</span></span>

<span data-ttu-id="49945-172">Домашняя страница по умолчанию является для посетителей логической отправной точкой при посещении веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="49945-172">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="49945-173">Для обслуживания страницы по умолчанию без указания полного имени URI вызовите метод [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) из `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="49945-173">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="49945-174">Для обслуживания файла по умолчанию метод `UseDefaultFiles`должен быть вызван до метода `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="49945-174">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="49945-175">Метод `UseDefaultFiles` фактически не обслуживает файл, а только перезаписывает URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="49945-175">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="49945-176">Для обслуживания файла включите ПО промежуточного слоя для статических файлов, вызвав метод `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="49945-176">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="49945-177">При вызове метода `UseDefaultFiles` запросы к папке будут искать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="49945-177">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="49945-178">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="49945-178">*default.htm*</span></span>
* <span data-ttu-id="49945-179">*default.html*</span><span class="sxs-lookup"><span data-stu-id="49945-179">*default.html*</span></span>
* <span data-ttu-id="49945-180">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="49945-180">*index.htm*</span></span>
* <span data-ttu-id="49945-181">*index.html*</span><span class="sxs-lookup"><span data-stu-id="49945-181">*index.html*</span></span>

<span data-ttu-id="49945-182">Первый найденный файл из списка будет обслужен, как будто был введен полный URI.</span><span class="sxs-lookup"><span data-stu-id="49945-182">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="49945-183">URL-адрес в браузере будет соответствовать запрошенному URI.</span><span class="sxs-lookup"><span data-stu-id="49945-183">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="49945-184">Следующий код позволяет изменить имя файла по умолчанию на *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="49945-184">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="49945-185">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="49945-185">UseFileServer</span></span>

<span data-ttu-id="49945-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> объединяет функции `UseStaticFiles`, `UseDefaultFiles` и при необходимости `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="49945-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="49945-187">Следующий пример кода позволяет обслуживать статические файлы и файл по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="49945-187">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="49945-188">Просмотр каталогов отключен.</span><span class="sxs-lookup"><span data-stu-id="49945-188">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="49945-189">Следующий код отличается от предыдущей перегрузки метода без параметров включением просмотра каталогов:</span><span class="sxs-lookup"><span data-stu-id="49945-189">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="49945-190">Пусть имеется следующая иерархия каталогов:</span><span class="sxs-lookup"><span data-stu-id="49945-190">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="49945-191">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="49945-191">**wwwroot**</span></span>
  * <span data-ttu-id="49945-192">**css**</span><span class="sxs-lookup"><span data-stu-id="49945-192">**css**</span></span>
  * <span data-ttu-id="49945-193">**images**</span><span class="sxs-lookup"><span data-stu-id="49945-193">**images**</span></span>
  * <span data-ttu-id="49945-194">**js**</span><span class="sxs-lookup"><span data-stu-id="49945-194">**js**</span></span>
* <span data-ttu-id="49945-195">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="49945-195">**MyStaticFiles**</span></span>
  * <span data-ttu-id="49945-196">**images**</span><span class="sxs-lookup"><span data-stu-id="49945-196">**images**</span></span>
    * <span data-ttu-id="49945-197">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="49945-197">*banner1.svg*</span></span>
  * <span data-ttu-id="49945-198">*default.html*</span><span class="sxs-lookup"><span data-stu-id="49945-198">*default.html*</span></span>

<span data-ttu-id="49945-199">Следующий пример кода включает обслуживание статических файлов, файлы по умолчанию и просмотр каталога `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="49945-199">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="49945-200">Метод `AddDirectoryBrowser` должен вызываться при значении `true` свойства `EnableDirectoryBrowsing`:</span><span class="sxs-lookup"><span data-stu-id="49945-200">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="49945-201">При указанных выше коде и иерархии файлов URL-адреса будут разрешаться следующим образом:</span><span class="sxs-lookup"><span data-stu-id="49945-201">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="49945-202">URI</span><span class="sxs-lookup"><span data-stu-id="49945-202">URI</span></span>            |                             <span data-ttu-id="49945-203">Ответ</span><span class="sxs-lookup"><span data-stu-id="49945-203">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="49945-204">*http://\<адрес_сервера>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="49945-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="49945-205">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="49945-205">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="49945-206">*http://\<адрес_сервера>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="49945-206">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="49945-207">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="49945-207">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="49945-208">Если в каталоге *MyStaticFiles* отсутствует файл с именем по умолчанию, то *http://\<адрес_сервера>/StaticFiles* возвращает список содержимого каталога с доступными для перехода ссылками:</span><span class="sxs-lookup"><span data-stu-id="49945-208">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Список статических файлов](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="49945-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> и <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> выполняют перенаправление на стороне клиента из `http://{SERVER ADDRESS}/StaticFiles` (без косой черты в конце) в `http://{SERVER ADDRESS}/StaticFiles/` (с косой чертой в конце).</span><span class="sxs-lookup"><span data-stu-id="49945-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="49945-211">Относительные URL-адреса в каталоге *StaticFiles* считаются недопустимыми без косой черты в конце.</span><span class="sxs-lookup"><span data-stu-id="49945-211">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="49945-212">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="49945-212">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="49945-213">Класс [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) содержит свойство `Mappings`, которое служит для сопоставления расширений файлов и типов содержимого MIME.</span><span class="sxs-lookup"><span data-stu-id="49945-213">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="49945-214">В следующем примере несколько расширений файлов регистрируются в известные типы MIME.</span><span class="sxs-lookup"><span data-stu-id="49945-214">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="49945-215">Расширение *.rtf* заменяется, а *.mp4* удаляется.</span><span class="sxs-lookup"><span data-stu-id="49945-215">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="49945-216">См. раздел [Типы содержимого MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="49945-216">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="49945-217">Нестандартные типы содержимого</span><span class="sxs-lookup"><span data-stu-id="49945-217">Non-standard content types</span></span>

<span data-ttu-id="49945-218">ПО промежуточного слоя для статических файлов воспринимает почти 400 известных типов содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="49945-218">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="49945-219">Если пользователь запрашивает файл неизвестного типа, ПО промежуточного слоя статических файлов передает запрос следующему компоненту ПО промежуточного слоя в конвейере.</span><span class="sxs-lookup"><span data-stu-id="49945-219">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="49945-220">Если ПО промежуточного слоя не удается обработать запрос, возвращается ответ *404 Не найдено*.</span><span class="sxs-lookup"><span data-stu-id="49945-220">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="49945-221">Если просмотр каталогов разрешен, то в списке каталогов отображается ссылка на файл.</span><span class="sxs-lookup"><span data-stu-id="49945-221">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="49945-222">Следующий код включает обслуживание неизвестных типов и обслуживает неизвестные файлы как изображения:</span><span class="sxs-lookup"><span data-stu-id="49945-222">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="49945-223">При выполнении вышеописанного кода ответ на запрос файла с неизвестным типом содержимого вернется в виде изображения.</span><span class="sxs-lookup"><span data-stu-id="49945-223">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="49945-224">Включение параметра [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) представляет угрозу безопасности.</span><span class="sxs-lookup"><span data-stu-id="49945-224">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="49945-225">По умолчанию он отключен, и его использование не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="49945-225">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="49945-226">Использование класса [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) является более безопасной альтернативой для обслуживания файлов с нестандартными расширениями.</span><span class="sxs-lookup"><span data-stu-id="49945-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="49945-227">Предоставление файлов из нескольких расположений</span><span class="sxs-lookup"><span data-stu-id="49945-227">Serve files from multiple locations</span></span>

<span data-ttu-id="49945-228">В качестве значений `UseStaticFiles` и `UseFileServer` по умолчанию используется поставщик файлов, указывающий на *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="49945-228">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="49945-229">Вы можете предоставить дополнительные экземпляры `UseStaticFiles` и `UseFileServer` с другими поставщиками файлов для предоставления файлов из других расположений.</span><span class="sxs-lookup"><span data-stu-id="49945-229">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="49945-230">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="49945-230">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="49945-231">Особенности</span><span class="sxs-lookup"><span data-stu-id="49945-231">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="49945-232">Использование `UseDirectoryBrowser` и `UseStaticFiles` может привести к утечке конфиденциальной информации.</span><span class="sxs-lookup"><span data-stu-id="49945-232">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="49945-233">Настоятельно рекомендуется отключать просмотр каталогов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="49945-233">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="49945-234">Тщательно проверьте, просмотр каких каталогов разрешен посредством `UseStaticFiles` или `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="49945-234">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="49945-235">Весь каталог и его подкаталоги становятся общедоступными.</span><span class="sxs-lookup"><span data-stu-id="49945-235">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="49945-236">Храните файлы, предназначенные для общего доступа, в выделенных каталогах, таких как *\<корневой_каталог_содержимого>/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="49945-236">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="49945-237">Отделите эти файлы от представлений MVC, страниц Razor Pages (только для версии 2.x), файлов конфигурации и т. д.</span><span class="sxs-lookup"><span data-stu-id="49945-237">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="49945-238">К URL-адресам содержимого, к которому предоставлен доступ методами `UseDirectoryBrowser` и `UseStaticFiles`, применяются те же требования по регистрозависимости и запрещенным символам, что и к базовой файловой системе.</span><span class="sxs-lookup"><span data-stu-id="49945-238">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="49945-239">Например, в Windows не учитывается регистр, а в macOS и Linux &mdash; учитывается.</span><span class="sxs-lookup"><span data-stu-id="49945-239">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="49945-240">Приложения ASP.NET Core, размещенные в IIS, используют [Модуль Core ASP.NET](xref:host-and-deploy/aspnet-core-module) для перенаправления всех запросов к приложению, включая запросы статических файлов.</span><span class="sxs-lookup"><span data-stu-id="49945-240">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="49945-241">Обработчик статических файлов IIS не используется.</span><span class="sxs-lookup"><span data-stu-id="49945-241">The IIS static file handler isn't used.</span></span> <span data-ttu-id="49945-242">Не существует возможности обработать запросы до того, как их обработает модуль.</span><span class="sxs-lookup"><span data-stu-id="49945-242">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="49945-243">Выполните следующие шаги в диспетчере служб IIS для удаления обработчика статических файлов IIS на уровне сервера или веб-сайта:</span><span class="sxs-lookup"><span data-stu-id="49945-243">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="49945-244">Перейдите к компоненту **Модули**.</span><span class="sxs-lookup"><span data-stu-id="49945-244">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="49945-245">Выберите в списке модуль **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="49945-245">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="49945-246">Нажмите кнопку **Удалить** в боковой панели **Действия**.</span><span class="sxs-lookup"><span data-stu-id="49945-246">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="49945-247">Если обработчик статических файлов IIS включен **и** модуль ASP.NET Core настроен неправильно, то статические файлы будут обслуживаться.</span><span class="sxs-lookup"><span data-stu-id="49945-247">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="49945-248">Это может случиться, если, например, не был развернут файл *web.config*.</span><span class="sxs-lookup"><span data-stu-id="49945-248">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="49945-249">Размещайте файлы с кодом (включая *CS* и *CSHTML*) за пределами [корневого каталога документов](xref:fundamentals/index#web-root) проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="49945-249">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="49945-250">Таким образом, в приложении создается логическое разделение между клиентским содержимым и серверным кодом.</span><span class="sxs-lookup"><span data-stu-id="49945-250">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="49945-251">Это предотвращает утечку серверного кода.</span><span class="sxs-lookup"><span data-stu-id="49945-251">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49945-252">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="49945-252">Additional resources</span></span>

* [<span data-ttu-id="49945-253">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="49945-253">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="49945-254">Введение в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49945-254">Introduction to ASP.NET Core</span></span>](xref:index)
