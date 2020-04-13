---
title: Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core
author: guardrex
description: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и страниц GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f364d94085d175fde5596c222ef21852c0106ec1
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751131"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="17565-103">Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17565-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="17565-104">Авторы: [Люк Лэтем](https://github.com/guardrex), [Рэйнер Стропек](https://www.timecockpit.com) и [Дэниэл Рот](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="17565-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="17565-105">С [моделью размещения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="17565-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="17565-106">Приложение Blazor, его зависимости и среда выполнения .NET скачиваются в браузере.</span><span class="sxs-lookup"><span data-stu-id="17565-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="17565-107">Приложение выполняется непосредственно в потоке пользовательского интерфейса браузера.</span><span class="sxs-lookup"><span data-stu-id="17565-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="17565-108">Поддерживаются следующие стратегии развертывания:</span><span class="sxs-lookup"><span data-stu-id="17565-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="17565-109">Приложение Blazor обслуживается приложением ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="17565-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="17565-110">Эта стратегия описана в разделе [Размещенное развертывание с использованием ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="17565-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="17565-111">Приложение Blazor помещается на статическом веб-сервере или службе, где .NET не используется, чтобы обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="17565-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="17565-112">Эта стратегия рассматривается в разделе [Автономное развертывание](#standalone-deployment), где приводятся сведения о размещении приложения Blazor WebAssembly в качестве дочернего приложения IIS.</span><span class="sxs-lookup"><span data-stu-id="17565-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="17565-113">Переопределение URL-адресов для маршрутизации</span><span class="sxs-lookup"><span data-stu-id="17565-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="17565-114">Маршрутизация запросов к компонентам страниц в приложении Blazor WebAssembly не настолько проста, как маршрутизация запросов к приложению, размещенному на сервере Blazor.</span><span class="sxs-lookup"><span data-stu-id="17565-114">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="17565-115">Рассмотрим приложение Blazor WebAssembly с двумя компонентами:</span><span class="sxs-lookup"><span data-stu-id="17565-115">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="17565-116">*Main.razor* &ndash; — загружается в корне приложения и содержит ссылку на компонент `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="17565-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="17565-117">*About.razor* &ndash; `About` — компонент.</span><span class="sxs-lookup"><span data-stu-id="17565-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="17565-118">При запросе документа приложения по умолчанию в адресной строке браузера (например, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="17565-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="17565-119">Браузер отправляет запрос.</span><span class="sxs-lookup"><span data-stu-id="17565-119">The browser makes a request.</span></span>
1. <span data-ttu-id="17565-120">Возвращается страница по умолчанию; обычно это *index.html*.</span><span class="sxs-lookup"><span data-stu-id="17565-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="17565-121">Страница *index.html* обеспечивает начальную загрузку приложения.</span><span class="sxs-lookup"><span data-stu-id="17565-121">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="17565-122">Загружается маршрутизатор Blazor, и компонент `Main` Razor преобразовывается для просмотра.</span><span class="sxs-lookup"><span data-stu-id="17565-122">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="17565-123">На странице Main возможность выбора ссылки на компонент `About` доступна на клиентском компьютере, так как маршрутизатор Blazor не разрешает браузеру сделать запрос в Интернете к `www.contoso.com` для `About` и сам обрабатывает отображенный компонент `About`.</span><span class="sxs-lookup"><span data-stu-id="17565-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="17565-124">Все запросы внутренних конечных точек *в пределах приложения Blazor WebAssembly* будут работать так же: Запросы не активируют браузерные запросы к ресурсам, размещенным на сервере в Интернете.</span><span class="sxs-lookup"><span data-stu-id="17565-124">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="17565-125">Маршрутизатор обрабатывает запросы внутренним образом.</span><span class="sxs-lookup"><span data-stu-id="17565-125">The router handles the requests internally.</span></span>

<span data-ttu-id="17565-126">Если запрос `www.contoso.com/About` сделан с помощью адресной строки браузера, он завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="17565-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="17565-127">Такой ресурс не существует на интернет-узле приложения, поэтому возвращается ответ *404 — Not Found* (не найдено).</span><span class="sxs-lookup"><span data-stu-id="17565-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="17565-128">Поскольку браузеры выполняют запросы к интернет-узлам для страниц на стороне клиента, веб-серверам и службам размещения необходимо переопределять все запросы к ресурсам, физически находящимся не на сервере, в случае страницы *index.html*.</span><span class="sxs-lookup"><span data-stu-id="17565-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="17565-129">Когда *index.html* возвращается, маршрутизатор Blazor приложения берет на себя управление и отвечает необходимым ресурсом.</span><span class="sxs-lookup"><span data-stu-id="17565-129">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="17565-130">При развертывании на сервере IIS можно использовать модуль переопределения URL-адресов с опубликованным файлом приложения *web.config*.</span><span class="sxs-lookup"><span data-stu-id="17565-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="17565-131">Дополнительные сведения см. в описании [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="17565-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="17565-132">Размещенное развертывание с использованием ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17565-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="17565-133">*Размещенное развертывание*  обслуживает приложение Blazor WebAssembly для браузеров из [приложения ASP.NET Core](xref:index), выполняющегося на веб-сервере.</span><span class="sxs-lookup"><span data-stu-id="17565-133">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="17565-134">Клиентское приложение Blazor WebAssembly публикуется в папку */bin/Release/{Целевая_платформа}/publish/wwwroot* серверного приложения вместе со всеми прочими статическими веб-ресурсами серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="17565-134">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="17565-135">Оба приложения развертываются вместе.</span><span class="sxs-lookup"><span data-stu-id="17565-135">The two apps are deployed together.</span></span> <span data-ttu-id="17565-136">Веб-сервер, позволяющий размещать приложения ASP.NET Core, является обязательным.</span><span class="sxs-lookup"><span data-stu-id="17565-136">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="17565-137">Для размещаемого развертывания Visual Studio включает шаблон проекта **приложения Blazor WebAssembly** (шаблон `blazorwasm` при использовании команды [dotnet new](/dotnet/core/tools/dotnet-new)) с выбранным параметром **Hosted** (`-ho|--hosted` при использовании команды `dotnet new`).</span><span class="sxs-lookup"><span data-stu-id="17565-137">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="17565-138">Дополнительные сведения о размещении приложения ASP.NET Core и его развертывании см. в разделе <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="17565-138">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="17565-139">Подробнее о развертывании в службе приложений Azure см. в <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="17565-139">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="17565-140">Автономное развертывание</span><span class="sxs-lookup"><span data-stu-id="17565-140">Standalone deployment</span></span>

<span data-ttu-id="17565-141">*Автономное развертывание* обрабатывает приложение Blazor WebAssembly как набор статических файлов, которые будут запрашиваться непосредственно клиентами.</span><span class="sxs-lookup"><span data-stu-id="17565-141">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="17565-142">Любой статический файловый сервер способен обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="17565-142">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="17565-143">Ресурсы автономного развертывания публикуются в папку *bin/Release/{Целевая_платформа}publish/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="17565-143">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="17565-144">IIS</span><span class="sxs-lookup"><span data-stu-id="17565-144">IIS</span></span>

<span data-ttu-id="17565-145">IIS можно использовать как полнофункциональный статический файловый сервер для приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="17565-145">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="17565-146">Чтобы настроить IIS для размещения Blazor, перейдите к разделу [Развертывание статического веб-сайта на IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="17565-146">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="17565-147">Опубликованные ресурсы создаются в папке */bin/Release/{целевая_платформа}/publish*.</span><span class="sxs-lookup"><span data-stu-id="17565-147">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="17565-148">Разместить содержимое папки *publish* на веб-сервере или в службе размещения.</span><span class="sxs-lookup"><span data-stu-id="17565-148">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="17565-149">web.config.</span><span class="sxs-lookup"><span data-stu-id="17565-149">web.config</span></span>

<span data-ttu-id="17565-150">При публикации проекта Blazor создается файл *web.config* со следующей конфигурацией IIS:</span><span class="sxs-lookup"><span data-stu-id="17565-150">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="17565-151">Типы MIME заданы для следующих расширений файлов:</span><span class="sxs-lookup"><span data-stu-id="17565-151">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="17565-152">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="17565-152">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="17565-153">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="17565-153">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="17565-154">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="17565-154">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="17565-155">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="17565-155">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="17565-156">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="17565-156">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="17565-157">Сжатие HTTP включено для следующих типов MIME:</span><span class="sxs-lookup"><span data-stu-id="17565-157">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="17565-158">Задаются правила модуля переопределения URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="17565-158">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="17565-159">Предоставление подкаталога для размещения статических ресурсов приложения (*wwwroot/{Запрошенный_путь}* ).</span><span class="sxs-lookup"><span data-stu-id="17565-159">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="17565-160">Создание резервной маршрутизации для одностраничного приложения, которая позволяет перенаправлять запросы, относящиеся к нефайловым ресурсам, к документу приложения по умолчанию в папке статических ресурсов (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="17565-160">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="17565-161">Использование пользовательского файла web.config</span><span class="sxs-lookup"><span data-stu-id="17565-161">Use a custom web.config</span></span>

<span data-ttu-id="17565-162">Чтобы использовать пользовательский файл *web.config*, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="17565-162">To use a custom *web.config* file:</span></span>

1. <span data-ttu-id="17565-163">Поместите пользовательский файл *web.config* в корневой каталог папки проекта.</span><span class="sxs-lookup"><span data-stu-id="17565-163">Place the custom *web.config* file at the root of the project folder.</span></span>
1. <span data-ttu-id="17565-164">Добавьте в файл проекта (*CSPROJ*) следующий целевой объект:</span><span class="sxs-lookup"><span data-stu-id="17565-164">Add the following target to the project file (*.csproj*):</span></span>

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> <span data-ttu-id="17565-165">Использование свойства MSBuild `<IsWebConfigTransformDisabled>` со значением `true` не поддерживается в приложениях Blazor WebAssembly, [так как оно предназначено для приложений ASP.NET Core, развернутых в IIS](xref:host-and-deploy/iis/index#webconfig-file).</span><span class="sxs-lookup"><span data-stu-id="17565-165">Use of the MSBuild property `<IsWebConfigTransformDisabled>` set to `true` isn't supported in Blazor WebAssembly apps [as it is for ASP.NET Core apps deployed to IIS](xref:host-and-deploy/iis/index#webconfig-file).</span></span> <span data-ttu-id="17565-166">Дополнительные сведения см. в разделе [Для предоставления пользовательского файла web.config Blazor WASM требуется целевой объект копирования (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).</span><span class="sxs-lookup"><span data-stu-id="17565-166">For more information, see [Copy target required to provide custom Blazor WASM web.config (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="17565-167">Установка модуля переопределения URL-адресов</span><span class="sxs-lookup"><span data-stu-id="17565-167">Install the URL Rewrite Module</span></span>

<span data-ttu-id="17565-168">[Модуль переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite) нужен, чтобы переопределять URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17565-168">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="17565-169">Модуль не устанавливается по умолчанию и недоступен для установки как компонент службы роли веб-сервера (IIS).</span><span class="sxs-lookup"><span data-stu-id="17565-169">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="17565-170">Модуль необходимо скачать с веб-сайта IIS.</span><span class="sxs-lookup"><span data-stu-id="17565-170">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="17565-171">Для его установки используйте установщик веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="17565-171">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="17565-172">Локально перейдите на [страницу скачивания модуля переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="17565-172">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="17565-173">В англоязычной версии выберите **WebPI** для скачивания установщика веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="17565-173">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="17565-174">Для других языков выберите подходящую архитектуру сервера (x86 или x64) для скачивания установщика.</span><span class="sxs-lookup"><span data-stu-id="17565-174">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="17565-175">Скопируйте установщик на сервер.</span><span class="sxs-lookup"><span data-stu-id="17565-175">Copy the installer to the server.</span></span> <span data-ttu-id="17565-176">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="17565-176">Run the installer.</span></span> <span data-ttu-id="17565-177">Нажмите кнопку **Установить** и примите условия лицензионного соглашения.</span><span class="sxs-lookup"><span data-stu-id="17565-177">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="17565-178">Перезагрузка сервера после завершения установки не требуется.</span><span class="sxs-lookup"><span data-stu-id="17565-178">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="17565-179">Настройка веб-сайта</span><span class="sxs-lookup"><span data-stu-id="17565-179">Configure the website</span></span>

<span data-ttu-id="17565-180">Установите для веб сайта **физический путь** к папке приложения.</span><span class="sxs-lookup"><span data-stu-id="17565-180">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="17565-181">Папка содержит:</span><span class="sxs-lookup"><span data-stu-id="17565-181">The folder contains:</span></span>

* <span data-ttu-id="17565-182">Файл *web.config*, который используется службами IIS для настройки веб-сайта, включая необходимые правила перенаправления и типы содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="17565-182">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="17565-183">Папку статических ресурсов приложения.</span><span class="sxs-lookup"><span data-stu-id="17565-183">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="17565-184">Размещение в качестве дочернего приложения IIS</span><span class="sxs-lookup"><span data-stu-id="17565-184">Host as an IIS sub-app</span></span>

<span data-ttu-id="17565-185">Если автономное приложение размещено как дочернее приложение IIS, выполните одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="17565-185">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="17565-186">Отключите наследуемый обработчик модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="17565-186">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="17565-187">Удалите обработчик в опубликованном файле *web.config* приложения Blazor, добавив раздел `<handlers>` в файл:</span><span class="sxs-lookup"><span data-stu-id="17565-187">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="17565-188">Отключите наследование раздела `<system.webServer>` от корневого (родительского) приложения, используя `<location>` со значением `false` для `inheritInChildApplications`:</span><span class="sxs-lookup"><span data-stu-id="17565-188">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="17565-189">Удаление обработчика или отключение наследования выполняется вместе с [настройкой базового пути приложения](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="17565-189">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="17565-190">Задайте базовый путь приложения в файле *index.html* приложения как псевдоним IIS, используемый при настройке дочернего приложения в IIS.</span><span class="sxs-lookup"><span data-stu-id="17565-190">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="17565-191">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="17565-191">Troubleshooting</span></span>

<span data-ttu-id="17565-192">Если получено сообщение *500 — Internal Server Error* (внутренняя ошибка сервера), а диспетчер служб IIS возвращает ошибки при попытке получить доступ к конфигурации веб сайта, убедитесь, что установлен модуль переопределения URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="17565-192">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="17565-193">Если модуль не установлен, IIS не может проанализировать файл *web.config*.</span><span class="sxs-lookup"><span data-stu-id="17565-193">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="17565-194">Это предотвращает загрузку конфигурации веб сайта диспетчером служб IIS и передачу статических файлов Blazor c веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="17565-194">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="17565-195">Дополнительные сведения об устранении неполадок развертывания в службах IIS см. в разделе <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="17565-195">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="17565-196">Хранилище Azure</span><span class="sxs-lookup"><span data-stu-id="17565-196">Azure Storage</span></span>

<span data-ttu-id="17565-197">Размещение статических файлов [службы хранилища Azure](/azure/storage/) предусматривает размещение бессерверного приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="17565-197">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="17565-198">Поддерживаются пользовательские доменные имена, сеть доставки содержимого Azure (CDN) и HTTPS.</span><span class="sxs-lookup"><span data-stu-id="17565-198">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="17565-199">Если служба BLOB-объектов настроена для размещения статических веб-сайтов в учетной записи хранения:</span><span class="sxs-lookup"><span data-stu-id="17565-199">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="17565-200">Задайте для параметра **Имя документа индекса** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="17565-200">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="17565-201">Задайте для параметра **Путь к документу с ошибкой** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="17565-201">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="17565-202">Компоненты Razor и другие конечные точки, не являющиеся файлами, не имеют физических путей в статическом содержимом, хранящемся в службе больших двоичных объектов.</span><span class="sxs-lookup"><span data-stu-id="17565-202">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="17565-203">При получении запроса для одного из этих ресурсов, который должен обрабатываться маршрутизатором Blazor, ошибка *404 - Объект не найден*, создаваемая службой BLOB-объектов, перенаправляет запрос, используя **путь к документу с ошибкой**.</span><span class="sxs-lookup"><span data-stu-id="17565-203">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="17565-204">При этом возвращается большой двоичный объект *index.html*, и маршрутизатор Blazor загружает и обрабатывает путь.</span><span class="sxs-lookup"><span data-stu-id="17565-204">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="17565-205">См. подробнее о [размещении статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="17565-205">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="17565-206">Nginx</span><span class="sxs-lookup"><span data-stu-id="17565-206">Nginx</span></span>

<span data-ttu-id="17565-207">Следующий файл *nginx.conf* упрощен для демонстрации того, как настроить Nginx для отправки файла *Index.html*, когда не удается найти соответствующий файл на диске.</span><span class="sxs-lookup"><span data-stu-id="17565-207">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="17565-208">Дополнительные сведения о конфигурации веб-сервера Nginx в рабочей среде см. в разделе [Создание файлов конфигурации NGINX Plus и NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="17565-208">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="17565-209">Nginx в Docker</span><span class="sxs-lookup"><span data-stu-id="17565-209">Nginx in Docker</span></span>

<span data-ttu-id="17565-210">Чтобы разместить Blazor в Docker с помощью Nginx, настройте в Dockerfile образ с Nginx на основе Alpine.</span><span class="sxs-lookup"><span data-stu-id="17565-210">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="17565-211">Измените Dockerfile, скопировав файл *nginx.config* в контейнер.</span><span class="sxs-lookup"><span data-stu-id="17565-211">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="17565-212">Добавьте одну строку в Dockerfile, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="17565-212">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="17565-213">Apache</span><span class="sxs-lookup"><span data-stu-id="17565-213">Apache</span></span>

<span data-ttu-id="17565-214">Чтобы развернуть приложение Blazor WebAssembly в CentOS 7 или более поздней версии, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="17565-214">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="17565-215">Создайте файл конфигурации Apache.</span><span class="sxs-lookup"><span data-stu-id="17565-215">Create the Apache configuration file.</span></span> <span data-ttu-id="17565-216">В следующем примере показан упрощенный файл конфигурации (*blazorapp.config*):</span><span class="sxs-lookup"><span data-stu-id="17565-216">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="17565-217">Поместите файл конфигурации Apache в каталог `/etc/httpd/conf.d/`. Это каталог конфигурации Apache по умолчанию в CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="17565-217">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="17565-218">Поместите файлы приложения в каталог `/var/www/blazorapp` (расположение, указанное в параметре `DocumentRoot` в файле конфигурации).</span><span class="sxs-lookup"><span data-stu-id="17565-218">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="17565-219">Перезапустите службу Apache.</span><span class="sxs-lookup"><span data-stu-id="17565-219">Restart the Apache service.</span></span>

<span data-ttu-id="17565-220">Дополнительные сведения см. в разделах [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) и [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="17565-220">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="17565-221">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="17565-221">GitHub Pages</span></span>

<span data-ttu-id="17565-222">Чтобы обеспечить переопределение URL-адресов, добавьте файл *404.html* со сценарием, который производит перенаправление на страницу *index.html*.</span><span class="sxs-lookup"><span data-stu-id="17565-222">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="17565-223">Пример реализации, предоставленный сообществом, см. в разделе [Одностраничные приложения для страниц GitHub](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages на сайте GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="17565-223">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="17565-224">Пример с использованием подхода сообщества можно увидеть в разделе [blazor-demo/blazor-demo.github.io на сайте GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([активный сайт](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="17565-224">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="17565-225">При использовании сайта проекта вместо сайта организации следует добавить или обновить тег `<base>` в файле *index.html*.</span><span class="sxs-lookup"><span data-stu-id="17565-225">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="17565-226">Задайте для имени репозитория GitHub значение атрибута `href` с косой чертой в конце (например, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="17565-226">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="17565-227">Значения конфигурации узла</span><span class="sxs-lookup"><span data-stu-id="17565-227">Host configuration values</span></span>

<span data-ttu-id="17565-228">[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) могут принимать следующие значения конфигурации узла в качестве аргументов командной строки во время выполнения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="17565-228">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="17565-229">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="17565-229">Content root</span></span>

<span data-ttu-id="17565-230">Аргумент `--contentroot` задает абсолютный путь к каталогу, где находятся файлы содержимого приложения ([корневой каталог содержимого](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="17565-230">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="17565-231">В следующих примерах `/content-root-path` — это путь к корневому каталогу содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="17565-231">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="17565-232">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="17565-232">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="17565-233">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="17565-233">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="17565-234">Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="17565-234">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="17565-235">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="17565-235">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="17565-236">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="17565-236">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="17565-237">Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="17565-237">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="17565-238">Базовый путь</span><span class="sxs-lookup"><span data-stu-id="17565-238">Path base</span></span>

<span data-ttu-id="17565-239">Аргумент `--pathbase` задает базовый путь приложения для локального выполнения с некорневым относительным путем URL (в `<base>` тег `href` задан с путем, отличным от `/` для промежуточной и рабочей сред).</span><span class="sxs-lookup"><span data-stu-id="17565-239">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="17565-240">В следующих примерах `/relative-URL-path` — это базовый путь приложения.</span><span class="sxs-lookup"><span data-stu-id="17565-240">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="17565-241">Дополнительные сведения см. в описании [базового пути приложения](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="17565-241">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="17565-242">В отличие от пути, заданного через `href` в теге `<base>`, не ставьте косую черту (`/`) при передаче значения аргумента `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="17565-242">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="17565-243">Если основной путь приложения задан в теге `<base>` как `<base href="/CoolApp/">` (включая косую черту в конце), передайте значение аргумента командной строки как `--pathbase=/CoolApp` (без косой черты в конце).</span><span class="sxs-lookup"><span data-stu-id="17565-243">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="17565-244">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="17565-244">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="17565-245">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="17565-245">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="17565-246">Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="17565-246">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="17565-247">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="17565-247">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="17565-248">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="17565-248">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="17565-249">Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="17565-249">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="17565-250">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="17565-250">URLs</span></span>

<span data-ttu-id="17565-251">Аргумент `--urls` задает IP-адреса или адреса узлов с портами и протоколами, по которым ожидаются запросы.</span><span class="sxs-lookup"><span data-stu-id="17565-251">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="17565-252">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="17565-252">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="17565-253">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="17565-253">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="17565-254">Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="17565-254">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="17565-255">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="17565-255">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="17565-256">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="17565-256">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="17565-257">Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="17565-257">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="17565-258">Настройка компоновщика</span><span class="sxs-lookup"><span data-stu-id="17565-258">Configure the Linker</span></span>

Blazor<span data-ttu-id="17565-259"> выполняет компоновку для промежуточного языка (IL) в каждой сборке выпуска, чтобы удалить ненужный промежуточный язык из выходных сборок.</span><span class="sxs-lookup"><span data-stu-id="17565-259"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="17565-260">Для получения дополнительной информации см. <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="17565-260">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
