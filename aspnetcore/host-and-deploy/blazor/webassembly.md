---
title: Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core
author: guardrex
description: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и страниц GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: e136a401beffe9cc7e29906b3631ab3f068b30fd
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967601"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="cc664-103">Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc664-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="cc664-104">Авторы: [Люк Лэтем](https://github.com/guardrex) (Luke Latham), [Рэйнер Стропек](https://www.timecockpit.com) (Rainer Stropek), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth), [Бен Адамс](https://twitter.com/ben_a_adams) (Ben Adams) и [Сафиа Абдалла](https://safia.rocks) (Safia Abdalla).</span><span class="sxs-lookup"><span data-stu-id="cc664-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="cc664-105">С [моделью размещения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="cc664-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="cc664-106">Приложение Blazor, его зависимости и среда выполнения .NET скачиваются в браузере параллельно.</span><span class="sxs-lookup"><span data-stu-id="cc664-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="cc664-107">Приложение выполняется непосредственно в потоке пользовательского интерфейса браузера.</span><span class="sxs-lookup"><span data-stu-id="cc664-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="cc664-108">Поддерживаются следующие стратегии развертывания:</span><span class="sxs-lookup"><span data-stu-id="cc664-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="cc664-109">Приложение Blazor обслуживается приложением ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc664-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="cc664-110">Эта стратегия описана в разделе [Размещенное развертывание с использованием ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="cc664-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="cc664-111">Приложение Blazor помещается на статическом веб-сервере или службе, где .NET не используется, чтобы обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="cc664-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="cc664-112">Эта стратегия рассматривается в разделе [Автономное развертывание](#standalone-deployment), где приводятся сведения о размещении приложения Blazor WebAssembly в качестве дочернего приложения IIS.</span><span class="sxs-lookup"><span data-stu-id="cc664-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="cc664-113">Предварительное сжатие Brotli</span><span class="sxs-lookup"><span data-stu-id="cc664-113">Brotli precompression</span></span>

<span data-ttu-id="cc664-114">При публикации приложения Blazor WebAssembly выходные данные предварительно сжимаются с использованием [алгоритма сжатия Brotli](https://tools.ietf.org/html/rfc7932) на самом верхнем уровне, чтобы уменьшить размер приложения и устранить необходимость в сжатии среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="cc664-114">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="cc664-115">Сведения о конфигурации сжатия *web.config* IIS см. в разделе [IIS: алгоритмы сжатия Brotli и Gzip](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="cc664-115">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="cc664-116">Переопределение URL-адресов для маршрутизации</span><span class="sxs-lookup"><span data-stu-id="cc664-116">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="cc664-117">Маршрутизация запросов к компонентам страниц в приложении Blazor WebAssembly не настолько проста, как маршрутизация запросов к приложению, размещенному на сервере Blazor.</span><span class="sxs-lookup"><span data-stu-id="cc664-117">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="cc664-118">Рассмотрим приложение Blazor WebAssembly с двумя компонентами:</span><span class="sxs-lookup"><span data-stu-id="cc664-118">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="cc664-119">*Main.razor* &ndash; — загружается в корне приложения и содержит ссылку на компонент `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="cc664-119">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="cc664-120">*About.razor* &ndash; `About` — компонент.</span><span class="sxs-lookup"><span data-stu-id="cc664-120">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="cc664-121">При запросе документа приложения по умолчанию в адресной строке браузера (например, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="cc664-121">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="cc664-122">Браузер отправляет запрос.</span><span class="sxs-lookup"><span data-stu-id="cc664-122">The browser makes a request.</span></span>
1. <span data-ttu-id="cc664-123">Возвращается страница по умолчанию; обычно это *index.html*.</span><span class="sxs-lookup"><span data-stu-id="cc664-123">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="cc664-124">Страница *index.html* обеспечивает начальную загрузку приложения.</span><span class="sxs-lookup"><span data-stu-id="cc664-124">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="cc664-125">Загружается маршрутизатор Blazor, и компонент `Main` Razor преобразовывается для просмотра.</span><span class="sxs-lookup"><span data-stu-id="cc664-125">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="cc664-126">На странице Main возможность выбора ссылки на компонент `About` доступна на клиентском компьютере, так как маршрутизатор Blazor не разрешает браузеру сделать запрос в Интернете к `www.contoso.com` для `About` и сам обрабатывает отображенный компонент `About`.</span><span class="sxs-lookup"><span data-stu-id="cc664-126">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="cc664-127">Все запросы внутренних конечных точек *в пределах приложения Blazor WebAssembly* будут работать так же: Запросы не активируют браузерные запросы к ресурсам, размещенным на сервере в Интернете.</span><span class="sxs-lookup"><span data-stu-id="cc664-127">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="cc664-128">Маршрутизатор обрабатывает запросы внутренним образом.</span><span class="sxs-lookup"><span data-stu-id="cc664-128">The router handles the requests internally.</span></span>

<span data-ttu-id="cc664-129">Если запрос `www.contoso.com/About` сделан с помощью адресной строки браузера, он завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="cc664-129">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="cc664-130">Такой ресурс не существует на интернет-узле приложения, поэтому возвращается ответ *404 — Not Found* (не найдено).</span><span class="sxs-lookup"><span data-stu-id="cc664-130">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="cc664-131">Поскольку браузеры выполняют запросы к интернет-узлам для страниц на стороне клиента, веб-серверам и службам размещения необходимо переопределять все запросы к ресурсам, физически находящимся не на сервере, в случае страницы *index.html*.</span><span class="sxs-lookup"><span data-stu-id="cc664-131">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="cc664-132">Когда *index.html* возвращается, маршрутизатор Blazor приложения берет на себя управление и отвечает необходимым ресурсом.</span><span class="sxs-lookup"><span data-stu-id="cc664-132">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="cc664-133">При развертывании на сервере IIS можно использовать модуль переопределения URL-адресов с опубликованным файлом приложения *web.config*.</span><span class="sxs-lookup"><span data-stu-id="cc664-133">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="cc664-134">Дополнительные сведения см. в описании [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="cc664-134">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="cc664-135">Размещенное развертывание с использованием ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc664-135">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="cc664-136">\*Размещенное развертывание \* обслуживает приложение Blazor WebAssembly для браузеров из [приложения ASP.NET Core](xref:index), выполняющегося на веб-сервере.</span><span class="sxs-lookup"><span data-stu-id="cc664-136">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="cc664-137">Клиентское приложение Blazor WebAssembly публикуется в папку */bin/Release/{ЦЕЛЕВАЯ_ПЛАТФОРМА}/publish/wwwroot* серверного приложения вместе со всеми прочими статическими веб-ресурсами серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="cc664-137">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="cc664-138">Оба приложения развертываются вместе.</span><span class="sxs-lookup"><span data-stu-id="cc664-138">The two apps are deployed together.</span></span> <span data-ttu-id="cc664-139">Веб-сервер, позволяющий размещать приложения ASP.NET Core, является обязательным.</span><span class="sxs-lookup"><span data-stu-id="cc664-139">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="cc664-140">Для размещаемого развертывания Visual Studio включает шаблон проекта **приложения Blazor WebAssembly** (шаблон `blazorwasm` при использовании команды [dotnet new](/dotnet/core/tools/dotnet-new)) с выбранным параметром **Hosted** (`-ho|--hosted` при использовании команды `dotnet new`).</span><span class="sxs-lookup"><span data-stu-id="cc664-140">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="cc664-141">Дополнительные сведения о размещении приложения ASP.NET Core и его развертывании см. в разделе <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="cc664-141">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="cc664-142">Подробнее о развертывании в службе приложений Azure см. в <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="cc664-142">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="cc664-143">Автономное развертывание</span><span class="sxs-lookup"><span data-stu-id="cc664-143">Standalone deployment</span></span>

<span data-ttu-id="cc664-144">*Автономное развертывание* обрабатывает приложение Blazor WebAssembly как набор статических файлов, которые будут запрашиваться непосредственно клиентами.</span><span class="sxs-lookup"><span data-stu-id="cc664-144">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="cc664-145">Любой статический файловый сервер способен обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="cc664-145">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="cc664-146">Ресурсы автономного развертывания публикуются в папку *bin/Release/{Целевая_платформа}publish/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="cc664-146">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="cc664-147">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="cc664-147">Azure App Service</span></span>

<span data-ttu-id="cc664-148">Приложения Blazor WebAssembly можно развернуть в службах приложений Azure в Windows. При этом приложение размещается в службах [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="cc664-148">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="cc664-149">Развертывание автономного приложения Blazor WebAssembly в службе приложений Azure для Linux в настоящее время не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="cc664-149">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="cc664-150">Сейчас образ сервера Linux для размещения приложения недоступен.</span><span class="sxs-lookup"><span data-stu-id="cc664-150">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="cc664-151">Ведется работа над реализацией этого сценария.</span><span class="sxs-lookup"><span data-stu-id="cc664-151">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="cc664-152">IIS</span><span class="sxs-lookup"><span data-stu-id="cc664-152">IIS</span></span>

<span data-ttu-id="cc664-153">IIS можно использовать как полнофункциональный статический файловый сервер для приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="cc664-153">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="cc664-154">Чтобы настроить IIS для размещения Blazor, см. раздел [Построение статического веб-сайта на IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="cc664-154">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="cc664-155">Опубликованные ресурсы создаются в папке */bin/Release/{целевая_платформа}/publish*.</span><span class="sxs-lookup"><span data-stu-id="cc664-155">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="cc664-156">Разместить содержимое папки *publish* на веб-сервере или в службе размещения.</span><span class="sxs-lookup"><span data-stu-id="cc664-156">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="cc664-157">web.config.</span><span class="sxs-lookup"><span data-stu-id="cc664-157">web.config</span></span>

<span data-ttu-id="cc664-158">При публикации проекта Blazor создается файл *web.config* со следующей конфигурацией IIS:</span><span class="sxs-lookup"><span data-stu-id="cc664-158">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="cc664-159">Типы MIME заданы для следующих расширений файлов:</span><span class="sxs-lookup"><span data-stu-id="cc664-159">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="cc664-160">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="cc664-160">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="cc664-161">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="cc664-161">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="cc664-162">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="cc664-162">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="cc664-163">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="cc664-163">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="cc664-164">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="cc664-164">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="cc664-165">Сжатие HTTP включено для следующих типов MIME:</span><span class="sxs-lookup"><span data-stu-id="cc664-165">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="cc664-166">Задаются правила модуля переопределения URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="cc664-166">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="cc664-167">Предоставление подкаталога для размещения статических ресурсов приложения (*wwwroot/{Запрошенный_путь}* ).</span><span class="sxs-lookup"><span data-stu-id="cc664-167">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="cc664-168">Создание резервной маршрутизации для одностраничного приложения, которая позволяет перенаправлять запросы, относящиеся к нефайловым ресурсам, к документу приложения по умолчанию в папке статических ресурсов (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="cc664-168">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="cc664-169">Использование пользовательского файла web.config</span><span class="sxs-lookup"><span data-stu-id="cc664-169">Use a custom web.config</span></span>

<span data-ttu-id="cc664-170">Чтобы применить пользовательский файл *web.config*, поместите пользовательский файл *web.config* в корневую папку проекта и опубликуйте проект.</span><span class="sxs-lookup"><span data-stu-id="cc664-170">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="cc664-171">Установка модуля переопределения URL-адресов</span><span class="sxs-lookup"><span data-stu-id="cc664-171">Install the URL Rewrite Module</span></span>

<span data-ttu-id="cc664-172">[Модуль переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite) нужен, чтобы переопределять URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="cc664-172">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="cc664-173">Модуль не устанавливается по умолчанию и недоступен для установки как компонент службы роли веб-сервера (IIS).</span><span class="sxs-lookup"><span data-stu-id="cc664-173">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="cc664-174">Модуль необходимо скачать с веб-сайта IIS.</span><span class="sxs-lookup"><span data-stu-id="cc664-174">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="cc664-175">Для его установки используйте установщик веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="cc664-175">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="cc664-176">Локально перейдите на [страницу скачивания модуля переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="cc664-176">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="cc664-177">В англоязычной версии выберите **WebPI** для скачивания установщика веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="cc664-177">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="cc664-178">Для других языков выберите подходящую архитектуру сервера (x86 или x64) для скачивания установщика.</span><span class="sxs-lookup"><span data-stu-id="cc664-178">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="cc664-179">Скопируйте установщик на сервер.</span><span class="sxs-lookup"><span data-stu-id="cc664-179">Copy the installer to the server.</span></span> <span data-ttu-id="cc664-180">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="cc664-180">Run the installer.</span></span> <span data-ttu-id="cc664-181">Нажмите кнопку **Установить** и примите условия лицензионного соглашения.</span><span class="sxs-lookup"><span data-stu-id="cc664-181">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="cc664-182">Перезагрузка сервера после завершения установки не требуется.</span><span class="sxs-lookup"><span data-stu-id="cc664-182">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="cc664-183">Настройка веб-сайта</span><span class="sxs-lookup"><span data-stu-id="cc664-183">Configure the website</span></span>

<span data-ttu-id="cc664-184">Установите для веб сайта **физический путь** к папке приложения.</span><span class="sxs-lookup"><span data-stu-id="cc664-184">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="cc664-185">Папка содержит:</span><span class="sxs-lookup"><span data-stu-id="cc664-185">The folder contains:</span></span>

* <span data-ttu-id="cc664-186">Файл *web.config*, который используется службами IIS для настройки веб-сайта, включая необходимые правила перенаправления и типы содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="cc664-186">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="cc664-187">Папку статических ресурсов приложения.</span><span class="sxs-lookup"><span data-stu-id="cc664-187">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="cc664-188">Размещение в качестве дочернего приложения IIS</span><span class="sxs-lookup"><span data-stu-id="cc664-188">Host as an IIS sub-app</span></span>

<span data-ttu-id="cc664-189">Если автономное приложение размещено как дочернее приложение IIS, выполните одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="cc664-189">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="cc664-190">Отключите наследуемый обработчик модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc664-190">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="cc664-191">Удалите обработчик из опубликованного файла приложения Blazor *web.config*, добавив в файл раздел `<handlers>`:</span><span class="sxs-lookup"><span data-stu-id="cc664-191">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="cc664-192">Отключите наследование раздела `<system.webServer>` от корневого (родительского) приложения, используя `<location>` со значением `false` для `inheritInChildApplications`:</span><span class="sxs-lookup"><span data-stu-id="cc664-192">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="cc664-193">Удаление обработчика или отключение наследования выполняется вместе с [настройкой базового пути приложения](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="cc664-193">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="cc664-194">Задайте базовый путь приложения в файле *index.html* приложения как псевдоним IIS, используемый при настройке дочернего приложения в IIS.</span><span class="sxs-lookup"><span data-stu-id="cc664-194">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="cc664-195">Алгоритмы сжатия Brotli и Gzip</span><span class="sxs-lookup"><span data-stu-id="cc664-195">Brotli and Gzip compression</span></span>

<span data-ttu-id="cc664-196">С помощью файла *web.config* можно настроить IIS для обслуживания ресурсов Blazor, сжатых с помощью алгоритмов Brotli или gzip.</span><span class="sxs-lookup"><span data-stu-id="cc664-196">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="cc664-197">Пример конфигурации см. в файле [web.config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="cc664-197">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="cc664-198">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="cc664-198">Troubleshooting</span></span>

<span data-ttu-id="cc664-199">Если получено сообщение *500 — Internal Server Error* (внутренняя ошибка сервера), а диспетчер служб IIS возвращает ошибки при попытке получить доступ к конфигурации веб сайта, убедитесь, что установлен модуль переопределения URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="cc664-199">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="cc664-200">Если модуль не установлен, IIS не может проанализировать файл *web.config*.</span><span class="sxs-lookup"><span data-stu-id="cc664-200">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="cc664-201">Это предотвращает загрузку конфигурации веб сайта диспетчером служб IIS и передачу статических файлов Blazor c веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="cc664-201">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="cc664-202">Дополнительные сведения об устранении неполадок развертывания в службах IIS см. в разделе <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="cc664-202">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="cc664-203">Хранилище Azure</span><span class="sxs-lookup"><span data-stu-id="cc664-203">Azure Storage</span></span>

<span data-ttu-id="cc664-204">Размещение статических файлов [службы хранилища Azure](/azure/storage/) предусматривает размещение бессерверного приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="cc664-204">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="cc664-205">Поддерживаются пользовательские доменные имена, сеть доставки содержимого Azure (CDN) и HTTPS.</span><span class="sxs-lookup"><span data-stu-id="cc664-205">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="cc664-206">Если служба BLOB-объектов настроена для размещения статических веб-сайтов в учетной записи хранения:</span><span class="sxs-lookup"><span data-stu-id="cc664-206">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="cc664-207">Задайте для параметра **Имя документа индекса** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="cc664-207">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="cc664-208">Задайте для параметра **Путь к документу с ошибкой** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="cc664-208">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="cc664-209">Компоненты Razor и другие конечные точки, не являющиеся файлами, не имеют физических путей в статическом содержимом, хранящемся в службе больших двоичных объектов.</span><span class="sxs-lookup"><span data-stu-id="cc664-209">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="cc664-210">При получении запроса для одного из этих ресурсов, который должен обрабатываться маршрутизатором Blazor, ошибка *404 — Not Found* (не найдено), создаваемая службой BLOB-объектов, перенаправляет запрос, используя **путь к документу с ошибкой**.</span><span class="sxs-lookup"><span data-stu-id="cc664-210">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="cc664-211">При этом возвращается большой двоичный объект *index.html*, и маршрутизатор Blazor загружает и обрабатывает путь.</span><span class="sxs-lookup"><span data-stu-id="cc664-211">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="cc664-212">См. подробнее о [размещении статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="cc664-212">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="cc664-213">Nginx</span><span class="sxs-lookup"><span data-stu-id="cc664-213">Nginx</span></span>

<span data-ttu-id="cc664-214">Следующий файл *nginx.conf* упрощен для демонстрации того, как настроить Nginx для отправки файла *Index.html*, когда не удается найти соответствующий файл на диске.</span><span class="sxs-lookup"><span data-stu-id="cc664-214">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="cc664-215">Дополнительные сведения о конфигурации веб-сервера Nginx в рабочей среде см. в разделе [Создание файлов конфигурации NGINX Plus и NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="cc664-215">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="cc664-216">Nginx в Docker</span><span class="sxs-lookup"><span data-stu-id="cc664-216">Nginx in Docker</span></span>

<span data-ttu-id="cc664-217">Чтобы разместить Blazor в Docker с помощью Nginx, настройте в Dockerfile образ с Nginx на основе Alpine.</span><span class="sxs-lookup"><span data-stu-id="cc664-217">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="cc664-218">Измените Dockerfile, скопировав файл *nginx.config* в контейнер.</span><span class="sxs-lookup"><span data-stu-id="cc664-218">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="cc664-219">Добавьте одну строку в Dockerfile, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="cc664-219">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="cc664-220">Apache</span><span class="sxs-lookup"><span data-stu-id="cc664-220">Apache</span></span>

<span data-ttu-id="cc664-221">Чтобы развернуть приложение Blazor WebAssembly в CentOS 7 или более поздней версии, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="cc664-221">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="cc664-222">Создайте файл конфигурации Apache.</span><span class="sxs-lookup"><span data-stu-id="cc664-222">Create the Apache configuration file.</span></span> <span data-ttu-id="cc664-223">В следующем примере показан упрощенный файл конфигурации (*blazorapp.config*):</span><span class="sxs-lookup"><span data-stu-id="cc664-223">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="cc664-224">Поместите файл конфигурации Apache в каталог `/etc/httpd/conf.d/`. Это каталог конфигурации Apache по умолчанию в CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="cc664-224">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="cc664-225">Поместите файлы приложения в каталог `/var/www/blazorapp` (расположение, указанное в параметре `DocumentRoot` в файле конфигурации).</span><span class="sxs-lookup"><span data-stu-id="cc664-225">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="cc664-226">Перезапустите службу Apache.</span><span class="sxs-lookup"><span data-stu-id="cc664-226">Restart the Apache service.</span></span>

<span data-ttu-id="cc664-227">Дополнительные сведения см. в разделах [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) и [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="cc664-227">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="cc664-228">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="cc664-228">GitHub Pages</span></span>

<span data-ttu-id="cc664-229">Чтобы обеспечить переопределение URL-адресов, добавьте файл *404.html* со сценарием, который производит перенаправление на страницу *index.html*.</span><span class="sxs-lookup"><span data-stu-id="cc664-229">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="cc664-230">Пример реализации, предоставленный сообществом, см. в разделе [Одностраничные приложения для страниц GitHub](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages на сайте GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="cc664-230">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="cc664-231">Пример с использованием подхода сообщества можно увидеть в разделе [blazor-demo/blazor-demo.github.io на сайте GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([активный сайт](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="cc664-231">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="cc664-232">При использовании сайта проекта вместо сайта организации следует добавить или обновить тег `<base>` в файле *index.html*.</span><span class="sxs-lookup"><span data-stu-id="cc664-232">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="cc664-233">Задайте для имени репозитория GitHub значение атрибута `href` с косой чертой в конце (например, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="cc664-233">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="cc664-234">Значения конфигурации узла</span><span class="sxs-lookup"><span data-stu-id="cc664-234">Host configuration values</span></span>

<span data-ttu-id="cc664-235">[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) могут принимать следующие значения конфигурации узла в качестве аргументов командной строки во время выполнения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="cc664-235">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="cc664-236">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="cc664-236">Content root</span></span>

<span data-ttu-id="cc664-237">Аргумент `--contentroot` задает абсолютный путь к каталогу, где находятся файлы содержимого приложения ([корневой каталог содержимого](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="cc664-237">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="cc664-238">В следующих примерах `/content-root-path` — это путь к корневому каталогу содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="cc664-238">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="cc664-239">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="cc664-239">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="cc664-240">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="cc664-240">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="cc664-241">Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="cc664-241">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="cc664-242">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="cc664-242">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="cc664-243">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="cc664-243">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="cc664-244">Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cc664-244">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="cc664-245">Базовый путь</span><span class="sxs-lookup"><span data-stu-id="cc664-245">Path base</span></span>

<span data-ttu-id="cc664-246">Аргумент `--pathbase` задает базовый путь приложения для локального выполнения с некорневым относительным путем URL (в `<base>` тег `href` задан с путем, отличным от `/` для промежуточной и рабочей сред).</span><span class="sxs-lookup"><span data-stu-id="cc664-246">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="cc664-247">В следующих примерах `/relative-URL-path` — это базовый путь приложения.</span><span class="sxs-lookup"><span data-stu-id="cc664-247">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="cc664-248">Дополнительные сведения см. в описании [базового пути приложения](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="cc664-248">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cc664-249">В отличие от пути, заданного через `href` в теге `<base>`, не ставьте косую черту (`/`) при передаче значения аргумента `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="cc664-249">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="cc664-250">Если основной путь приложения задан в теге `<base>` как `<base href="/CoolApp/">` (включая косую черту в конце), передайте значение аргумента командной строки как `--pathbase=/CoolApp` (без косой черты в конце).</span><span class="sxs-lookup"><span data-stu-id="cc664-250">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="cc664-251">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="cc664-251">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="cc664-252">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="cc664-252">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="cc664-253">Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="cc664-253">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="cc664-254">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="cc664-254">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="cc664-255">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="cc664-255">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="cc664-256">Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cc664-256">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="cc664-257">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="cc664-257">URLs</span></span>

<span data-ttu-id="cc664-258">Аргумент `--urls` задает IP-адреса или адреса узлов с портами и протоколами, по которым ожидаются запросы.</span><span class="sxs-lookup"><span data-stu-id="cc664-258">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="cc664-259">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="cc664-259">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="cc664-260">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="cc664-260">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="cc664-261">Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="cc664-261">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="cc664-262">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="cc664-262">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="cc664-263">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="cc664-263">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="cc664-264">Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cc664-264">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="cc664-265">Настройка компоновщика</span><span class="sxs-lookup"><span data-stu-id="cc664-265">Configure the Linker</span></span>

<span data-ttu-id="cc664-266">Blazor выполняет компоновку для промежуточного языка (IL) в каждой сборке выпуска, чтобы удалить ненужный промежуточный язык из выходных сборок.</span><span class="sxs-lookup"><span data-stu-id="cc664-266">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="cc664-267">Для получения дополнительной информации см. <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="cc664-267">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="cc664-268">Загрузка пользовательских ресурсов загрузки</span><span class="sxs-lookup"><span data-stu-id="cc664-268">Custom boot resource loading</span></span>

<span data-ttu-id="cc664-269">Приложение Blazor WebAssembly можно инициализировать с помощью функции `loadBootResource`, чтобы переопределить встроенный механизм загрузки ресурсов загрузки.</span><span class="sxs-lookup"><span data-stu-id="cc664-269">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="cc664-270">Используйте `loadBootResource` в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="cc664-270">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="cc664-271">Разрешить пользователям загружать статические ресурсы, такие как данные о часовом поясе или *dotnet.wasm*, из сети CDN.</span><span class="sxs-lookup"><span data-stu-id="cc664-271">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="cc664-272">Загрузить сжатые сборки с помощью HTTP-запроса и распаковать их на клиенте для узлов, которые не поддерживают получение сжатого содержимого с сервера.</span><span class="sxs-lookup"><span data-stu-id="cc664-272">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="cc664-273">Создать псевдоним ресурсов, выбрав другое имя путем перенаправления каждого запроса `fetch` на новое имя.</span><span class="sxs-lookup"><span data-stu-id="cc664-273">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="cc664-274">Параметры `loadBootResource` приведены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="cc664-274">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="cc664-275">Параметр</span><span class="sxs-lookup"><span data-stu-id="cc664-275">Parameter</span></span>    | <span data-ttu-id="cc664-276">Описание</span><span class="sxs-lookup"><span data-stu-id="cc664-276">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="cc664-277">Тип ресурса.</span><span class="sxs-lookup"><span data-stu-id="cc664-277">The type of the resource.</span></span> <span data-ttu-id="cc664-278">Допустимые типы: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="cc664-278">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="cc664-279">Имя ресурса.</span><span class="sxs-lookup"><span data-stu-id="cc664-279">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="cc664-280">Относительный или абсолютный URI ресурса.</span><span class="sxs-lookup"><span data-stu-id="cc664-280">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="cc664-281">Строка целостности, представляющая ожидаемое содержимое в ответе.</span><span class="sxs-lookup"><span data-stu-id="cc664-281">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="cc664-282">`loadBootResource` возвращает любой из следующих элементов для переопределения процесса загрузки.</span><span class="sxs-lookup"><span data-stu-id="cc664-282">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="cc664-283">Строка URI.</span><span class="sxs-lookup"><span data-stu-id="cc664-283">URI string.</span></span> <span data-ttu-id="cc664-284">В следующем примере (*wwwroot/index.html*) следующие файлы обслуживаются из сети CDN в `https://my-awesome-cdn.com/`.</span><span class="sxs-lookup"><span data-stu-id="cc664-284">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="cc664-285">*dotnet.\*.js*</span><span class="sxs-lookup"><span data-stu-id="cc664-285">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="cc664-286">*dotnet.wasm*</span><span class="sxs-lookup"><span data-stu-id="cc664-286">*dotnet.wasm*</span></span>
  * <span data-ttu-id="cc664-287">Данные часового пояса</span><span class="sxs-lookup"><span data-stu-id="cc664-287">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="cc664-288">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="cc664-288">`Promise<Response>`.</span></span> <span data-ttu-id="cc664-289">Передайте параметр `integrity` в заголовке для сохранения поведения проверки целостности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="cc664-289">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="cc664-290">В следующем примере (*wwwroot/index.html*) в исходящие запросы добавляется пользовательский заголовок HTTP и передается параметр `integrity` в вызов `fetch`.</span><span class="sxs-lookup"><span data-stu-id="cc664-290">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="cc664-291">`null`/`undefined`, что приводит к поведению загрузки по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="cc664-291">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="cc664-292">Внешние источники должны возвращать необходимые заголовки CORS для браузеров, чтобы разрешить загрузку ресурсов между источниками.</span><span class="sxs-lookup"><span data-stu-id="cc664-292">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="cc664-293">По умолчанию CDN обычно предоставляют необходимые заголовки.</span><span class="sxs-lookup"><span data-stu-id="cc664-293">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="cc664-294">Необходимо указать только типы для пользовательских поведений.</span><span class="sxs-lookup"><span data-stu-id="cc664-294">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="cc664-295">Типы, не указанные в `loadBootResource`, загружаются платформой в соответствии с поведением при загрузке по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="cc664-295">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="cc664-296">Изменение расширения DLL-файлов.</span><span class="sxs-lookup"><span data-stu-id="cc664-296">Change the filename extension of DLL files</span></span>

<span data-ttu-id="cc664-297">Если вам необходимо изменить расширения опубликованных *DLL*-файлов приложения, следуйте указаниям в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="cc664-297">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="cc664-298">После публикации приложения используйте скрипт оболочки или конвейер сборки DevOps для переименования *DLL*-файлов для использования другого расширения файла.</span><span class="sxs-lookup"><span data-stu-id="cc664-298">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="cc664-299">Указывайте на *DLL*-файлы в каталоге *wwwroot* опубликованных выходных данных приложения (например, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="cc664-299">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="cc664-300">В следующих примерах *DLL*-файлы переименованы для использования расширения файла *BIN*.</span><span class="sxs-lookup"><span data-stu-id="cc664-300">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="cc664-301">Windows:</span><span class="sxs-lookup"><span data-stu-id="cc664-301">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="cc664-302">В Linux или macOS.</span><span class="sxs-lookup"><span data-stu-id="cc664-302">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```
   
<span data-ttu-id="cc664-303">Чтобы использовать расширение файла, отличное от *BIN*, замените *BIN* в предыдущих командах.</span><span class="sxs-lookup"><span data-stu-id="cc664-303">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="cc664-304">Для сжатых файлов *blazor.boot.json.gz* и *blazor.boot.json.br* используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="cc664-304">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="cc664-305">Удалите сжатые файлы *blazor.boot.json.gz* и *blazor.boot.json.br*.</span><span class="sxs-lookup"><span data-stu-id="cc664-305">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="cc664-306">Сжатие при таком подходе отключается.</span><span class="sxs-lookup"><span data-stu-id="cc664-306">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="cc664-307">Выполните повторное сжатие обновленного файла *blazor.boot.json*.</span><span class="sxs-lookup"><span data-stu-id="cc664-307">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="cc664-308">В следующем примере Windows используется скрипт PowerShell, размещенный в корне проекта.</span><span class="sxs-lookup"><span data-stu-id="cc664-308">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="cc664-309">*ChangeDLLExtensions.ps1:* .</span><span class="sxs-lookup"><span data-stu-id="cc664-309">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="cc664-310">В файле проекта скрипт запускается после публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="cc664-310">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="cc664-311">Чтобы оставить отзыв, перейдите на страницу [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="cc664-311">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
