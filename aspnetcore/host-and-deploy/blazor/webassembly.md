---
title: "title: 'Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core' author: guardrex description: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и GitHub Pages."
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.автор: riande ms.custom: mvc ms.дата: 28.05.2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 09f74edaa3d1cb0d51e0ce8d0209383885b81f5f
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239390"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="eaff6-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eaff6-103">'Blazor'</span></span>

<span data-ttu-id="eaff6-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eaff6-104">'Identity'</span></span>

<span data-ttu-id="eaff6-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eaff6-105">'Let's Encrypt'</span></span>

* <span data-ttu-id="eaff6-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eaff6-106">'Razor'</span></span>
* <span data-ttu-id="eaff6-107">'SignalR' uid: host-and-deploy/blazor/webassembly</span><span class="sxs-lookup"><span data-stu-id="eaff6-107">'SignalR' uid: host-and-deploy/blazor/webassembly</span></span>

<span data-ttu-id="eaff6-108">Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eaff6-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

* <span data-ttu-id="eaff6-109">Авторы: [Люк Лэтем](https://github.com/guardrex) (Luke Latham), [Рэйнер Стропек](https://www.timecockpit.com) (Rainer Stropek), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth), [Бен Адамс](https://twitter.com/ben_a_adams) (Ben Adams) и [Сафиа Абдалла](https://safia.rocks) (Safia Abdalla).</span><span class="sxs-lookup"><span data-stu-id="eaff6-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span> <span data-ttu-id="eaff6-110">С [моделью размещения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="eaff6-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>
* <span data-ttu-id="eaff6-111">Приложение Blazor, его зависимости и среда выполнения .NET скачиваются в браузере параллельно.</span><span class="sxs-lookup"><span data-stu-id="eaff6-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span> <span data-ttu-id="eaff6-112">Приложение выполняется непосредственно в потоке пользовательского интерфейса браузера.</span><span class="sxs-lookup"><span data-stu-id="eaff6-112">The app is executed directly on the browser UI thread.</span></span>

## <a name="precompression"></a><span data-ttu-id="eaff6-113">Поддерживаются следующие стратегии развертывания:</span><span class="sxs-lookup"><span data-stu-id="eaff6-113">The following deployment strategies are supported:</span></span>

<span data-ttu-id="eaff6-114">Приложение Blazor обслуживается приложением ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eaff6-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="eaff6-115">Эта стратегия описана в разделе [Размещенное развертывание с использованием ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="eaff6-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>

* <span data-ttu-id="eaff6-116">Приложение Blazor помещается на статическом веб-сервере или службе, где .NET не используется, чтобы обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="eaff6-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span>
* <span data-ttu-id="eaff6-117">Эта стратегия рассматривается в разделе [Автономное развертывание](#standalone-deployment), где приводятся сведения о размещении приложения Blazor WebAssembly в качестве дочернего приложения IIS.</span><span class="sxs-lookup"><span data-stu-id="eaff6-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

<span data-ttu-id="eaff6-118">Предварительное сжатие</span><span class="sxs-lookup"><span data-stu-id="eaff6-118">Precompression</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="eaff6-119">При публикации приложения Blazor WebAssembly выходные данные предварительно сжимаются, чтобы уменьшить размер приложения и не сжимать среду выполнения.</span><span class="sxs-lookup"><span data-stu-id="eaff6-119">When a Blazor WebAssembly app is published, the output is precompressed to reduce the app's size and remove the need for runtime compression.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="eaff6-120">Используются следующие алгоритмы сжатия:</span><span class="sxs-lookup"><span data-stu-id="eaff6-120">The following compression algorithms are used:</span></span>

<span data-ttu-id="eaff6-121">[Brotli](https://tools.ietf.org/html/rfc7932) (высший уровень)</span><span class="sxs-lookup"><span data-stu-id="eaff6-121">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span> [<span data-ttu-id="eaff6-122">Gzip</span><span class="sxs-lookup"><span data-stu-id="eaff6-122">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

* <span data-ttu-id="eaff6-123">Чтобы отключить сжатие, добавьте свойство `BlazorEnableCompression` MSBuild в файл проекта приложения и задайте для него значение `false`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-123">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>
* <span data-ttu-id="eaff6-124">Сведения о конфигурации сжатия *web.config* IIS см. в разделе [IIS: алгоритмы сжатия Brotli и Gzip](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="eaff6-124">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

<span data-ttu-id="eaff6-125">Переопределение URL-адресов для маршрутизации</span><span class="sxs-lookup"><span data-stu-id="eaff6-125">Rewrite URLs for correct routing</span></span>

1. <span data-ttu-id="eaff6-126">Маршрутизация запросов к компонентам страниц в приложении Blazor WebAssembly не настолько проста, как маршрутизация запросов к приложению, размещенному на сервере Blazor.</span><span class="sxs-lookup"><span data-stu-id="eaff6-126">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span>
1. <span data-ttu-id="eaff6-127">Рассмотрим приложение Blazor WebAssembly с двумя компонентами:</span><span class="sxs-lookup"><span data-stu-id="eaff6-127">Consider a Blazor WebAssembly app with two components:</span></span>
1. <span data-ttu-id="eaff6-128">*Main.razor*: загружается в корне приложения и содержит ссылку на компонент `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="eaff6-128">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
1. <span data-ttu-id="eaff6-129">*About.razor* — компонент `About`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-129">*About.razor*: `About` component.</span></span>

<span data-ttu-id="eaff6-130">При запросе документа приложения по умолчанию в адресной строке браузера (например, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="eaff6-130">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span> <span data-ttu-id="eaff6-131">Браузер отправляет запрос.</span><span class="sxs-lookup"><span data-stu-id="eaff6-131">The browser makes a request.</span></span> <span data-ttu-id="eaff6-132">Возвращается страница по умолчанию; обычно это *index.html*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-132">The default page is returned, which is usually *index.html*.</span></span>

<span data-ttu-id="eaff6-133">Страница *index.html* обеспечивает начальную загрузку приложения.</span><span class="sxs-lookup"><span data-stu-id="eaff6-133">*index.html* bootstraps the app.</span></span> <span data-ttu-id="eaff6-134">Загружается маршрутизатор Blazor, и компонент Razor `Main` преобразовывается для просмотра.</span><span class="sxs-lookup"><span data-stu-id="eaff6-134">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="eaff6-135">На странице Main возможность выбора ссылки на компонент `About` доступна на клиентском компьютере, так как маршрутизатор Blazor не разрешает браузеру сделать запрос в Интернете к `www.contoso.com` для `About` и сам обрабатывает отображенный компонент `About`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-135">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="eaff6-136">Все запросы внутренних конечных точек *в пределах приложения Blazor WebAssembly* будут работать так же: Запросы не активируют браузерные запросы к ресурсам, размещенным на сервере в Интернете.</span><span class="sxs-lookup"><span data-stu-id="eaff6-136">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span>

<span data-ttu-id="eaff6-137">Маршрутизатор обрабатывает запросы внутренним образом.</span><span class="sxs-lookup"><span data-stu-id="eaff6-137">The router handles the requests internally.</span></span> <span data-ttu-id="eaff6-138">Если запрос `www.contoso.com/About` сделан с помощью адресной строки браузера, он завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="eaff6-138">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="eaff6-139">Такой ресурс не существует на интернет-узле приложения, поэтому возвращается ответ *404 — Not Found* (не найдено).</span><span class="sxs-lookup"><span data-stu-id="eaff6-139">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="eaff6-140">Поскольку браузеры выполняют запросы к интернет-узлам для страниц на стороне клиента, веб-серверам и службам размещения необходимо переопределять все запросы к ресурсам, физически находящимся не на сервере, в случае страницы *index.html*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-140">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span>

<span data-ttu-id="eaff6-141">Когда *index.html* возвращается, маршрутизатор Blazor приложения берет на себя управление и отвечает необходимым ресурсом.</span><span class="sxs-lookup"><span data-stu-id="eaff6-141">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span> <span data-ttu-id="eaff6-142">При развертывании на сервере IIS можно использовать модуль переопределения URL-адресов с опубликованным файлом приложения *web.config*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-142">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="eaff6-143">Дополнительные сведения см. в описании [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="eaff6-143">For more information, see the [IIS](#iis) section.</span></span> <span data-ttu-id="eaff6-144">Размещенное развертывание с использованием ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eaff6-144">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="eaff6-145">*Размещенное развертывание*  обслуживает приложение Blazor WebAssembly для браузеров из [приложения ASP.NET Core](xref:index), выполняющегося на веб-сервере.</span><span class="sxs-lookup"><span data-stu-id="eaff6-145">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="eaff6-146">Клиентское приложение Blazor WebAssembly публикуется в папку */bin/Release/{Целевая_платформа}/publish/wwwroot* серверного приложения вместе со всеми прочими статическими веб-ресурсами серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="eaff6-146">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="eaff6-147">Оба приложения развертываются вместе.</span><span class="sxs-lookup"><span data-stu-id="eaff6-147">The two apps are deployed together.</span></span>

<span data-ttu-id="eaff6-148">Веб-сервер, позволяющий размещать приложения ASP.NET Core, является обязательным.</span><span class="sxs-lookup"><span data-stu-id="eaff6-148">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="eaff6-149">Для размещаемого развертывания Visual Studio включает шаблон проекта **приложения Blazor WebAssembly** (шаблон `blazorwasm` при использовании команды [dotnet new](/dotnet/core/tools/dotnet-new)) с выбранным параметром **Hosted** (`-ho|--hosted` при использовании команды `dotnet new`).</span><span class="sxs-lookup"><span data-stu-id="eaff6-149">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="eaff6-150">Дополнительные сведения о размещении приложения ASP.NET Core и его развертывании см. в разделе <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="eaff6-150">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="eaff6-151">Подробнее о развертывании в службе приложений Azure см. в <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="eaff6-151">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="eaff6-152">Автономное развертывание</span><span class="sxs-lookup"><span data-stu-id="eaff6-152">Standalone deployment</span></span>

<span data-ttu-id="eaff6-153">*Автономное развертывание* обрабатывает приложение Blazor WebAssembly как набор статических файлов, которые будут запрашиваться непосредственно клиентами.</span><span class="sxs-lookup"><span data-stu-id="eaff6-153">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="eaff6-154">Любой статический файловый сервер способен обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="eaff6-154">Any static file server is able to serve the Blazor app.</span></span> <span data-ttu-id="eaff6-155">Ресурсы автономного развертывания публикуются в папку *bin/Release/{Целевая_платформа}publish/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-155">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="eaff6-156">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="eaff6-156">Azure App Service</span></span>

<span data-ttu-id="eaff6-157">Приложения Blazor WebAssembly можно развернуть в службах приложений Azure в Windows. При этом приложение размещается в службах [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="eaff6-157">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span> <span data-ttu-id="eaff6-158">Развертывание автономного приложения Blazor WebAssembly в Службе приложений Azure для Linux в настоящее время не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="eaff6-158">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span>

<span data-ttu-id="eaff6-159">Сейчас образ сервера Linux для размещения приложения недоступен.</span><span class="sxs-lookup"><span data-stu-id="eaff6-159">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="eaff6-160">Ведется работа над реализацией этого сценария.</span><span class="sxs-lookup"><span data-stu-id="eaff6-160">Work is in progress to enable this scenario.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="eaff6-161">IIS</span><span class="sxs-lookup"><span data-stu-id="eaff6-161">IIS</span></span>

<span data-ttu-id="eaff6-162">IIS можно использовать как полнофункциональный статический файловый сервер для приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="eaff6-162">IIS is a capable static file server for Blazor apps.</span></span>

* <span data-ttu-id="eaff6-163">Чтобы настроить IIS для размещения Blazor, перейдите к разделу [Развертывание статического веб-сайта на IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="eaff6-163">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>
  * <span data-ttu-id="eaff6-164">Опубликованные ресурсы создаются в папке */bin/Release/{целевая_платформа}/publish*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-164">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>
  * <span data-ttu-id="eaff6-165">Разместить содержимое папки *publish* на веб-сервере или в службе размещения.</span><span class="sxs-lookup"><span data-stu-id="eaff6-165">Host the contents of the *publish* folder on the web server or hosting service.</span></span>
  * <span data-ttu-id="eaff6-166">web.config.</span><span class="sxs-lookup"><span data-stu-id="eaff6-166">web.config</span></span>
  * <span data-ttu-id="eaff6-167">При публикации проекта Blazor создается файл *web.config* со следующей конфигурацией IIS:</span><span class="sxs-lookup"><span data-stu-id="eaff6-167">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>
  * <span data-ttu-id="eaff6-168">Типы MIME заданы для следующих расширений файлов:</span><span class="sxs-lookup"><span data-stu-id="eaff6-168">MIME types are set for the following file extensions:</span></span>
* <span data-ttu-id="eaff6-169">*.dll*: `application/octet-stream`;</span><span class="sxs-lookup"><span data-stu-id="eaff6-169">*.dll*: `application/octet-stream`</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="eaff6-170">*.json*: `application/json`;</span><span class="sxs-lookup"><span data-stu-id="eaff6-170">*.json*: `application/json`</span></span>
  * <span data-ttu-id="eaff6-171">*.wasm*: `application/wasm`;</span><span class="sxs-lookup"><span data-stu-id="eaff6-171">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="eaff6-172">*.woff*: `application/font-woff`;</span><span class="sxs-lookup"><span data-stu-id="eaff6-172">*.woff*: `application/font-woff`</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="eaff6-173">*.woff2*: `application/font-woff`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-173">*.woff2*: `application/font-woff`</span></span>

<span data-ttu-id="eaff6-174">Сжатие HTTP включено для следующих типов MIME:</span><span class="sxs-lookup"><span data-stu-id="eaff6-174">HTTP compression is enabled for the following MIME types:</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="eaff6-175">Задаются правила модуля переопределения URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="eaff6-175">URL Rewrite Module rules are established:</span></span>

<span data-ttu-id="eaff6-176">Предоставление подкаталога для размещения статических ресурсов приложения (*wwwroot/{Запрошенный_путь}* ).</span><span class="sxs-lookup"><span data-stu-id="eaff6-176">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span> <span data-ttu-id="eaff6-177">Создание резервной маршрутизации для одностраничного приложения, которая позволяет перенаправлять запросы, относящиеся к нефайловым ресурсам, к документу приложения по умолчанию в папке статических ресурсов (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="eaff6-177">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span> <span data-ttu-id="eaff6-178">Использование пользовательского файла web.config</span><span class="sxs-lookup"><span data-stu-id="eaff6-178">Use a custom web.config</span></span> <span data-ttu-id="eaff6-179">Чтобы применить пользовательский файл *web.config*, поместите пользовательский файл *web.config* в корневую папку проекта и опубликуйте проект.</span><span class="sxs-lookup"><span data-stu-id="eaff6-179">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

1. <span data-ttu-id="eaff6-180">Установка модуля переопределения URL-адресов</span><span class="sxs-lookup"><span data-stu-id="eaff6-180">Install the URL Rewrite Module</span></span> <span data-ttu-id="eaff6-181">[Модуль переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite) нужен, чтобы переопределять URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="eaff6-181">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="eaff6-182">Модуль не устанавливается по умолчанию и недоступен для установки как компонент службы роли веб-сервера (IIS).</span><span class="sxs-lookup"><span data-stu-id="eaff6-182">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span>
1. <span data-ttu-id="eaff6-183">Модуль необходимо скачать с веб-сайта IIS.</span><span class="sxs-lookup"><span data-stu-id="eaff6-183">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="eaff6-184">Для его установки используйте установщик веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="eaff6-184">Use the Web Platform Installer to install the module:</span></span> <span data-ttu-id="eaff6-185">Локально перейдите на [страницу скачивания модуля переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="eaff6-185">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="eaff6-186">В англоязычной версии выберите **WebPI** для скачивания установщика веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="eaff6-186">For the English version, select **WebPI** to download the WebPI installer.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="eaff6-187">Для других языков выберите подходящую архитектуру сервера (x86 или x64) для скачивания установщика.</span><span class="sxs-lookup"><span data-stu-id="eaff6-187">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>

<span data-ttu-id="eaff6-188">Скопируйте установщик на сервер.</span><span class="sxs-lookup"><span data-stu-id="eaff6-188">Copy the installer to the server.</span></span> <span data-ttu-id="eaff6-189">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="eaff6-189">Run the installer.</span></span>

* <span data-ttu-id="eaff6-190">Нажмите кнопку **Установить** и примите условия лицензионного соглашения.</span><span class="sxs-lookup"><span data-stu-id="eaff6-190">Select the **Install** button and accept the license terms.</span></span>
* <span data-ttu-id="eaff6-191">Перезагрузка сервера после завершения установки не требуется.</span><span class="sxs-lookup"><span data-stu-id="eaff6-191">A server restart isn't required after the install completes.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="eaff6-192">Настройка веб-сайта</span><span class="sxs-lookup"><span data-stu-id="eaff6-192">Configure the website</span></span>

<span data-ttu-id="eaff6-193">Установите для веб сайта **физический путь** к папке приложения.</span><span class="sxs-lookup"><span data-stu-id="eaff6-193">Set the website's **Physical path** to the app's folder.</span></span>

* <span data-ttu-id="eaff6-194">Папка содержит:</span><span class="sxs-lookup"><span data-stu-id="eaff6-194">The folder contains:</span></span>

  <span data-ttu-id="eaff6-195">Файл *web.config*, который используется службами IIS для настройки веб-сайта, включая необходимые правила перенаправления и типы содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="eaff6-195">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="eaff6-196">Папку статических ресурсов приложения.</span><span class="sxs-lookup"><span data-stu-id="eaff6-196">The app's static asset folder.</span></span>

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

<span data-ttu-id="eaff6-197">Размещение в качестве дочернего приложения IIS</span><span class="sxs-lookup"><span data-stu-id="eaff6-197">Host as an IIS sub-app</span></span> <span data-ttu-id="eaff6-198">Если автономное приложение размещено как дочернее приложение IIS, выполните одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="eaff6-198">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="eaff6-199">Отключите наследуемый обработчик модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eaff6-199">Disable the inherited ASP.NET Core Module handler.</span></span>

<span data-ttu-id="eaff6-200">Удалите обработчик в опубликованном файле *web.config* приложения Blazor, добавив раздел `<handlers>` в файл:</span><span class="sxs-lookup"><span data-stu-id="eaff6-200">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span> <span data-ttu-id="eaff6-201">Отключите наследование раздела `<system.webServer>` от корневого (родительского) приложения, используя `<location>` со значением `false` для `inheritInChildApplications`:</span><span class="sxs-lookup"><span data-stu-id="eaff6-201">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="eaff6-202">Удаление обработчика или отключение наследования выполняется вместе с [настройкой базового пути приложения](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="eaff6-202">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

<span data-ttu-id="eaff6-203">Задайте базовый путь приложения в файле *index.html* приложения как псевдоним IIS, используемый при настройке дочернего приложения в IIS.</span><span class="sxs-lookup"><span data-stu-id="eaff6-203">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span> <span data-ttu-id="eaff6-204">Алгоритмы сжатия Brotli и Gzip</span><span class="sxs-lookup"><span data-stu-id="eaff6-204">Brotli and Gzip compression</span></span> <span data-ttu-id="eaff6-205">С помощью файла *web.config* можно настроить IIS для обслуживания ресурсов Blazor, сжатых с помощью алгоритмов Brotli или gzip.</span><span class="sxs-lookup"><span data-stu-id="eaff6-205">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span>

<span data-ttu-id="eaff6-206">Пример конфигурации см. в файле [web.config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="eaff6-206">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

### <a name="azure-storage"></a><span data-ttu-id="eaff6-207">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="eaff6-207">Troubleshooting</span></span>

<span data-ttu-id="eaff6-208">Если получено сообщение *500 — Internal Server Error* (внутренняя ошибка сервера), а диспетчер служб IIS возвращает ошибки при попытке получить доступ к конфигурации веб сайта, убедитесь, что установлен модуль переопределения URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="eaff6-208">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="eaff6-209">Если модуль не установлен, IIS не может проанализировать файл *web.config*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-209">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span>

<span data-ttu-id="eaff6-210">Это предотвращает загрузку конфигурации веб сайта диспетчером служб IIS и передачу статических файлов Blazor c веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="eaff6-210">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

* <span data-ttu-id="eaff6-211">Дополнительные сведения об устранении неполадок развертывания в службах IIS см. в разделе <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="eaff6-211">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>
* <span data-ttu-id="eaff6-212">Хранилище Azure</span><span class="sxs-lookup"><span data-stu-id="eaff6-212">Azure Storage</span></span> <span data-ttu-id="eaff6-213">Размещение статических файлов [службы хранилища Azure](/azure/storage/) предусматривает размещение бессерверного приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="eaff6-213">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="eaff6-214">Поддерживаются пользовательские доменные имена, сеть доставки содержимого Azure (CDN) и HTTPS.</span><span class="sxs-lookup"><span data-stu-id="eaff6-214">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span> <span data-ttu-id="eaff6-215">Если служба BLOB-объектов настроена для размещения статических веб-сайтов в учетной записи хранения:</span><span class="sxs-lookup"><span data-stu-id="eaff6-215">When the blob service is enabled for static website hosting on a storage account:</span></span>

<span data-ttu-id="eaff6-216">Задайте для параметра **Имя документа индекса** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-216">Set the **Index document name** to `index.html`.</span></span>

* <span data-ttu-id="eaff6-217">Задайте для параметра **Путь к документу с ошибкой** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-217">Set the **Error document path** to `index.html`.</span></span>
* <span data-ttu-id="eaff6-218">Компоненты Razor и другие конечные точки, не являющиеся файлами, не имеют физических путей в статическом содержимом, хранящемся в службе BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="eaff6-218">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span>

  <span data-ttu-id="eaff6-219">При получении запроса для одного из этих ресурсов, который должен обрабатываться маршрутизатором Blazor, ошибка *404 - Объект не найден*, создаваемая службой BLOB-объектов, перенаправляет запрос, используя **путь к документу с ошибкой**.</span><span class="sxs-lookup"><span data-stu-id="eaff6-219">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span>
  
  1. <span data-ttu-id="eaff6-220">При этом возвращается большой двоичный объект *index.html*, и маршрутизатор Blazor загружает и обрабатывает путь.</span><span class="sxs-lookup"><span data-stu-id="eaff6-220">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>
  1. <span data-ttu-id="eaff6-221">Если файлы не загружаются во время выполнения из-за недопустимых типов MIME в заголовках файлов `Content-Type`, выполните одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="eaff6-221">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

<span data-ttu-id="eaff6-222">Настройте средства для установки правильных типов MIME (заголовков `Content-Type`) при развертывании файлов.</span><span class="sxs-lookup"><span data-stu-id="eaff6-222">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>

### <a name="nginx"></a><span data-ttu-id="eaff6-223">Измените типы MIME (заголовки `Content-Type`) для файлов после развертывания приложения.</span><span class="sxs-lookup"><span data-stu-id="eaff6-223">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

<span data-ttu-id="eaff6-224">В Обозревателе службы хранилища (портал Azure) для каждого файла сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="eaff6-224">In Storage Explorer (Azure portal) for each file:</span></span>

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

<span data-ttu-id="eaff6-225">Щелкните правой кнопкой мыши файл и выберите **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="eaff6-225">Right-click the file and select **Properties**.</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="eaff6-226">Укажите значение для параметра **ContentType** и нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="eaff6-226">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="eaff6-227">См. подробнее о [размещении статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="eaff6-227">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span> <span data-ttu-id="eaff6-228">Nginx</span><span class="sxs-lookup"><span data-stu-id="eaff6-228">Nginx</span></span>

<span data-ttu-id="eaff6-229">Следующий файл *nginx.conf* упрощен для демонстрации того, как настроить Nginx для отправки файла *Index.html*, когда не удается найти соответствующий файл на диске.</span><span class="sxs-lookup"><span data-stu-id="eaff6-229">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="eaff6-230">Дополнительные сведения о конфигурации веб-сервера Nginx в рабочей среде см. в разделе [Создание файлов конфигурации NGINX Plus и NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="eaff6-230">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

<span data-ttu-id="eaff6-231">Nginx в Docker</span><span class="sxs-lookup"><span data-stu-id="eaff6-231">Nginx in Docker</span></span>

1. <span data-ttu-id="eaff6-232">Чтобы разместить Blazor в Docker с помощью Nginx, настройте в Dockerfile образ с Nginx на основе Alpine.</span><span class="sxs-lookup"><span data-stu-id="eaff6-232">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="eaff6-233">Измените Dockerfile, скопировав файл *nginx.config* в контейнер.</span><span class="sxs-lookup"><span data-stu-id="eaff6-233">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

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

1. <span data-ttu-id="eaff6-234">Добавьте одну строку в Dockerfile, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="eaff6-234">Add one line to the Dockerfile, as shown in the following example:</span></span>

1. <span data-ttu-id="eaff6-235">Apache</span><span class="sxs-lookup"><span data-stu-id="eaff6-235">Apache</span></span>

1. <span data-ttu-id="eaff6-236">Чтобы развернуть приложение Blazor WebAssembly в CentOS 7 или более поздней версии, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="eaff6-236">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

<span data-ttu-id="eaff6-237">Создайте файл конфигурации Apache.</span><span class="sxs-lookup"><span data-stu-id="eaff6-237">Create the Apache configuration file.</span></span>

### <a name="github-pages"></a><span data-ttu-id="eaff6-238">В следующем примере показан упрощенный файл конфигурации (*blazorapp.config*):</span><span class="sxs-lookup"><span data-stu-id="eaff6-238">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

<span data-ttu-id="eaff6-239">Поместите файл конфигурации Apache в каталог `/etc/httpd/conf.d/`. Это каталог конфигурации Apache по умолчанию в CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="eaff6-239">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span> <span data-ttu-id="eaff6-240">Поместите файлы приложения в каталог `/var/www/blazorapp` (расположение, указанное в параметре `DocumentRoot` в файле конфигурации).</span><span class="sxs-lookup"><span data-stu-id="eaff6-240">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span> <span data-ttu-id="eaff6-241">Перезапустите службу Apache.</span><span class="sxs-lookup"><span data-stu-id="eaff6-241">Restart the Apache service.</span></span>

<span data-ttu-id="eaff6-242">Дополнительные сведения см. в разделах [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) и [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="eaff6-242">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span> <span data-ttu-id="eaff6-243">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="eaff6-243">GitHub Pages</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="eaff6-244">Чтобы обеспечить переопределение URL-адресов, добавьте файл *404.html* со сценарием, который производит перенаправление на страницу *index.html*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-244">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span>

<span data-ttu-id="eaff6-245">Пример реализации, предоставленный сообществом, см. в разделе [Одностраничные приложения для страниц GitHub](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages на сайте GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="eaff6-245">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span>

### <a name="content-root"></a><span data-ttu-id="eaff6-246">Пример с использованием подхода сообщества можно увидеть в разделе [blazor-demo/blazor-demo.github.io на сайте GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([активный сайт](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="eaff6-246">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="eaff6-247">При использовании сайта проекта вместо сайта организации следует добавить или обновить тег `<base>` в файле *index.html*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-247">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="eaff6-248">Задайте для имени репозитория GitHub значение атрибута `href` с косой чертой в конце (например, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="eaff6-248">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

* <span data-ttu-id="eaff6-249">Значения конфигурации узла</span><span class="sxs-lookup"><span data-stu-id="eaff6-249">Host configuration values</span></span> <span data-ttu-id="eaff6-250">[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) могут принимать следующие значения конфигурации узла в качестве аргументов командной строки во время выполнения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="eaff6-250">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="eaff6-251">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="eaff6-251">Content root</span></span> <span data-ttu-id="eaff6-252">Аргумент `--contentroot` задает абсолютный путь к каталогу, где находятся файлы содержимого приложения ([корневой каталог содержимого](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="eaff6-252">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="eaff6-253">В следующих примерах `/content-root-path` — это путь к корневому каталогу содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="eaff6-253">In the following examples, `/content-root-path` is the app's content root path.</span></span> <span data-ttu-id="eaff6-254">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="eaff6-254">Pass the argument when running the app locally at a command prompt.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="eaff6-255">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="eaff6-255">From the app's directory, execute:</span></span>

<span data-ttu-id="eaff6-256">Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="eaff6-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="eaff6-257">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-257">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span> <span data-ttu-id="eaff6-258">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="eaff6-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="eaff6-259">Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span> <span data-ttu-id="eaff6-260">Базовый путь</span><span class="sxs-lookup"><span data-stu-id="eaff6-260">Path base</span></span>

* <span data-ttu-id="eaff6-261">Аргумент `--pathbase` задает базовый путь приложения для локального выполнения с некорневым относительным путем URL (в `<base>` тег `href` задан с путем, отличным от `/` для промежуточной и рабочей сред).</span><span class="sxs-lookup"><span data-stu-id="eaff6-261">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="eaff6-262">В следующих примерах `/relative-URL-path` — это базовый путь приложения.</span><span class="sxs-lookup"><span data-stu-id="eaff6-262">In the following examples, `/relative-URL-path` is the app's path base.</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="eaff6-263">Дополнительные сведения см. в описании [базового пути приложения](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="eaff6-263">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="eaff6-264">В отличие от пути, заданного через `href` в теге `<base>`, не ставьте косую черту (`/`) при передаче значения аргумента `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-264">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="eaff6-265">Если основной путь приложения задан в теге `<base>` как `<base href="/CoolApp/">` (включая косую черту в конце), передайте значение аргумента командной строки как `--pathbase=/CoolApp` (без косой черты в конце).</span><span class="sxs-lookup"><span data-stu-id="eaff6-265">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span> <span data-ttu-id="eaff6-266">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="eaff6-266">Pass the argument when running the app locally at a command prompt.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="eaff6-267">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="eaff6-267">From the app's directory, execute:</span></span>

<span data-ttu-id="eaff6-268">Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="eaff6-268">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span>

* <span data-ttu-id="eaff6-269">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-269">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span> <span data-ttu-id="eaff6-270">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="eaff6-270">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="eaff6-271">Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-271">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span> <span data-ttu-id="eaff6-272">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="eaff6-272">URLs</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="eaff6-273">Аргумент `--urls` задает IP-адреса или адреса узлов с портами и протоколами, по которым ожидаются запросы.</span><span class="sxs-lookup"><span data-stu-id="eaff6-273">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span> <span data-ttu-id="eaff6-274">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="eaff6-274">Pass the argument when running the app locally at a command prompt.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="eaff6-275">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="eaff6-275">From the app's directory, execute:</span></span>

<span data-ttu-id="eaff6-276">Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="eaff6-276">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="eaff6-277">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-277">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="eaff6-278">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="eaff6-278">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span>

<span data-ttu-id="eaff6-279">Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-279">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span> <span data-ttu-id="eaff6-280">Настройка компоновщика</span><span class="sxs-lookup"><span data-stu-id="eaff6-280">Configure the Linker</span></span>

* Blazor<span data-ttu-id="eaff6-281"> выполняет компоновку для промежуточного языка (IL) в каждой сборке выпуска, чтобы удалить ненужный промежуточный язык из выходных сборок.</span><span class="sxs-lookup"><span data-stu-id="eaff6-281"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span>
* <span data-ttu-id="eaff6-282">Для получения дополнительной информации см. <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="eaff6-282">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
* <span data-ttu-id="eaff6-283">Загрузка пользовательских ресурсов загрузки</span><span class="sxs-lookup"><span data-stu-id="eaff6-283">Custom boot resource loading</span></span>

<span data-ttu-id="eaff6-284">Приложение Blazor WebAssembly можно инициализировать с помощью функции `loadBootResource`, чтобы переопределить встроенный механизм загрузки ресурсов загрузки.</span><span class="sxs-lookup"><span data-stu-id="eaff6-284">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span>

| <span data-ttu-id="eaff6-285">Используйте `loadBootResource` в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="eaff6-285">Use `loadBootResource` for the following scenarios:</span></span>    | <span data-ttu-id="eaff6-286">Разрешить пользователям загружать статические ресурсы, такие как данные о часовом поясе или *dotnet.wasm*, из сети CDN.</span><span class="sxs-lookup"><span data-stu-id="eaff6-286">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="eaff6-287">Загрузить сжатые сборки с помощью HTTP-запроса и распаковать их на клиенте для узлов, которые не поддерживают получение сжатого содержимого с сервера.</span><span class="sxs-lookup"><span data-stu-id="eaff6-287">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span> <span data-ttu-id="eaff6-288">Создать псевдоним ресурсов, выбрав другое имя путем перенаправления каждого запроса `fetch` на новое имя.</span><span class="sxs-lookup"><span data-stu-id="eaff6-288">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span> |
| `name`       | <span data-ttu-id="eaff6-289">Параметры `loadBootResource` приведены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="eaff6-289">`loadBootResource` parameters appear in the following table.</span></span> |
| `defaultUri` | <span data-ttu-id="eaff6-290">Параметр</span><span class="sxs-lookup"><span data-stu-id="eaff6-290">Parameter</span></span> |
| `integrity`  | <span data-ttu-id="eaff6-291">Описание</span><span class="sxs-lookup"><span data-stu-id="eaff6-291">Description</span></span> |

<span data-ttu-id="eaff6-292">Тип ресурса.</span><span class="sxs-lookup"><span data-stu-id="eaff6-292">The type of the resource.</span></span>

* <span data-ttu-id="eaff6-293">Допустимые типы: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="eaff6-293">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> <span data-ttu-id="eaff6-294">Имя ресурса.</span><span class="sxs-lookup"><span data-stu-id="eaff6-294">The name of the resource.</span></span>

  * <span data-ttu-id="eaff6-295">Относительный или абсолютный URI ресурса.</span><span class="sxs-lookup"><span data-stu-id="eaff6-295">The relative or absolute URI of the resource.</span></span>
  * <span data-ttu-id="eaff6-296">Строка целостности, представляющая ожидаемое содержимое в ответе.</span><span class="sxs-lookup"><span data-stu-id="eaff6-296">The integrity string representing the expected content in the response.</span></span>
  * <span data-ttu-id="eaff6-297">`loadBootResource` возвращает любой из следующих элементов для переопределения процесса загрузки.</span><span class="sxs-lookup"><span data-stu-id="eaff6-297">`loadBootResource` returns any of the following to override the loading process:</span></span>

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

* <span data-ttu-id="eaff6-298">Строка URI.</span><span class="sxs-lookup"><span data-stu-id="eaff6-298">URI string.</span></span> <span data-ttu-id="eaff6-299">В следующем примере (*wwwroot/index.html*) следующие файлы обслуживаются из сети CDN в `https://my-awesome-cdn.com/`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-299">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  <span data-ttu-id="eaff6-300">*dotnet.\*.js*</span><span class="sxs-lookup"><span data-stu-id="eaff6-300">*dotnet.\*.js*</span></span>
  
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

* <span data-ttu-id="eaff6-301">*dotnet.wasm*</span><span class="sxs-lookup"><span data-stu-id="eaff6-301">*dotnet.wasm*</span></span>

<span data-ttu-id="eaff6-302">Данные часового пояса</span><span class="sxs-lookup"><span data-stu-id="eaff6-302">Timezone data</span></span> <span data-ttu-id="eaff6-303">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-303">`Promise<Response>`.</span></span>

<span data-ttu-id="eaff6-304">Передайте параметр `integrity` в заголовке для сохранения поведения проверки целостности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="eaff6-304">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span> <span data-ttu-id="eaff6-305">В следующем примере (*wwwroot/index.html*) в исходящие запросы добавляется пользовательский заголовок HTTP и передается параметр `integrity` в вызов `fetch`.</span><span class="sxs-lookup"><span data-stu-id="eaff6-305">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="eaff6-306">`null`/`undefined`, что приводит к поведению загрузки по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="eaff6-306">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="eaff6-307">Внешние источники должны возвращать необходимые заголовки CORS для браузеров, чтобы разрешить загрузку ресурсов между источниками.</span><span class="sxs-lookup"><span data-stu-id="eaff6-307">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span>

<span data-ttu-id="eaff6-308">По умолчанию CDN обычно предоставляют необходимые заголовки.</span><span class="sxs-lookup"><span data-stu-id="eaff6-308">CDNs usually provide the required headers by default.</span></span> <span data-ttu-id="eaff6-309">Необходимо указать только типы для пользовательских поведений.</span><span class="sxs-lookup"><span data-stu-id="eaff6-309">You only need to specify types for custom behaviors.</span></span>

<span data-ttu-id="eaff6-310">Типы, не указанные в `loadBootResource`, загружаются платформой в соответствии с поведением при загрузке по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="eaff6-310">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

<span data-ttu-id="eaff6-311">Изменение расширения DLL-файлов.</span><span class="sxs-lookup"><span data-stu-id="eaff6-311">Change the filename extension of DLL files</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="eaff6-312">Если вам необходимо изменить расширения опубликованных *DLL*-файлов приложения, следуйте указаниям в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="eaff6-312">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="eaff6-313">После публикации приложения используйте скрипт оболочки или конвейер сборки DevOps для переименования *DLL*-файлов для использования другого расширения файла.</span><span class="sxs-lookup"><span data-stu-id="eaff6-313">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="eaff6-314">Указывайте на *DLL*-файлы в каталоге *wwwroot* опубликованных выходных данных приложения (например, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="eaff6-314">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="eaff6-315">В следующих примерах *DLL*-файлы переименованы для использования расширения файла *BIN*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-315">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="eaff6-316">Windows:</span><span class="sxs-lookup"><span data-stu-id="eaff6-316">On Windows:</span></span>

* <span data-ttu-id="eaff6-317">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="eaff6-317">If service worker assets are also in use, add the following command:</span></span> <span data-ttu-id="eaff6-318">В Linux или macOS.</span><span class="sxs-lookup"><span data-stu-id="eaff6-318">On Linux or macOS:</span></span>
* <span data-ttu-id="eaff6-319">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="eaff6-319">If service worker assets are also in use, add the following command:</span></span>

<span data-ttu-id="eaff6-320">Чтобы использовать расширение файла, отличное от *BIN*, замените *BIN* в предыдущих командах.</span><span class="sxs-lookup"><span data-stu-id="eaff6-320">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span> <span data-ttu-id="eaff6-321">Для сжатых файлов *blazor.boot.json.gz* и *blazor.boot.json.br* используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="eaff6-321">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span> <span data-ttu-id="eaff6-322">Удалите сжатые файлы *blazor.boot.json.gz* и *blazor.boot.json.br*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-322">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span>

<span data-ttu-id="eaff6-323">Сжатие при таком подходе отключается.</span><span class="sxs-lookup"><span data-stu-id="eaff6-323">Compression is disabled with this approach.</span></span>

<span data-ttu-id="eaff6-324">Выполните повторное сжатие обновленного файла *blazor.boot.json*.</span><span class="sxs-lookup"><span data-stu-id="eaff6-324">Recompress the updated *blazor.boot.json* file.</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="eaff6-325">Приведенные выше рекомендации также применимы при использовании ресурсов рабочей роли.</span><span class="sxs-lookup"><span data-stu-id="eaff6-325">The preceding guidance also applies when service worker assets are in use.</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="eaff6-326">Удалите или повторно распакуйте файлы *wwwroot/service-worker-assets.js.br* и *wwwroot/service-worker-assets.js.gz*,</span><span class="sxs-lookup"><span data-stu-id="eaff6-326">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="eaff6-327">иначе проверка целостности файлов в браузере завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="eaff6-327">Otherwise, file integrity checks fail in the browser.</span></span>
 
