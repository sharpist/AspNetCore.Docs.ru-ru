---
<span data-ttu-id="ebbfd-101">title: Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core автор: описание: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и GitHub Pages.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-101">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ebbfd-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebbfd-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-103">'Blazor'</span></span>
- <span data-ttu-id="ebbfd-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-104">'Identity'</span></span>
- <span data-ttu-id="ebbfd-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebbfd-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-106">'Razor'</span></span>
- <span data-ttu-id="ebbfd-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="ebbfd-107">'SignalR' uid:</span></span> 

---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="ebbfd-108">Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebbfd-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="ebbfd-109">Авторы: [Люк Лэтем](https://github.com/guardrex) (Luke Latham), [Рэйнер Стропек](https://www.timecockpit.com) (Rainer Stropek), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth), [Бен Адамс](https://twitter.com/ben_a_adams) (Ben Adams) и [Сафиа Абдалла](https://safia.rocks) (Safia Abdalla).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="ebbfd-110">С [моделью размещения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="ebbfd-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="ebbfd-111">Приложение Blazor, его зависимости и среда выполнения .NET скачиваются в браузере параллельно.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="ebbfd-112">Приложение выполняется непосредственно в потоке пользовательского интерфейса браузера.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-112">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="ebbfd-113">Поддерживаются следующие стратегии развертывания:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-113">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="ebbfd-114">Приложение Blazor обслуживается приложением ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="ebbfd-115">Эта стратегия описана в разделе [Размещенное развертывание с использованием ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="ebbfd-116">Приложение Blazor помещается на статическом веб-сервере или службе, где .NET не используется, чтобы обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="ebbfd-117">Эта стратегия рассматривается в разделе [Автономное развертывание](#standalone-deployment), где приводятся сведения о размещении приложения Blazor WebAssembly в качестве дочернего приложения IIS.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="ebbfd-118">Предварительное сжатие Brotli</span><span class="sxs-lookup"><span data-stu-id="ebbfd-118">Brotli precompression</span></span>

<span data-ttu-id="ebbfd-119">При публикации приложения Blazor WebAssembly выходные данные предварительно сжимаются с использованием [алгоритма сжатия Brotli](https://tools.ietf.org/html/rfc7932) на самом верхнем уровне, чтобы уменьшить размер приложения и устранить необходимость в сжатии среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-119">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="ebbfd-120">Сведения о конфигурации сжатия *web.config* IIS см. в разделе [IIS: алгоритмы сжатия Brotli и Gzip](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-120">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="ebbfd-121">Переопределение URL-адресов для маршрутизации</span><span class="sxs-lookup"><span data-stu-id="ebbfd-121">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="ebbfd-122">Маршрутизация запросов к компонентам страниц в приложении Blazor WebAssembly не настолько проста, как маршрутизация запросов к приложению, размещенному на сервере Blazor.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-122">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="ebbfd-123">Рассмотрим приложение Blazor WebAssembly с двумя компонентами:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-123">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="ebbfd-124">*Main.razor*: загружается в корне приложения и содержит ссылку на компонент `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-124">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="ebbfd-125">*About.razor* — компонент `About`.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-125">*About.razor*: `About` component.</span></span>

<span data-ttu-id="ebbfd-126">При запросе документа приложения по умолчанию в адресной строке браузера (например, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="ebbfd-126">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="ebbfd-127">Браузер отправляет запрос.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-127">The browser makes a request.</span></span>
1. <span data-ttu-id="ebbfd-128">Возвращается страница по умолчанию; обычно это *index.html*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-128">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="ebbfd-129">Страница *index.html* обеспечивает начальную загрузку приложения.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-129">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="ebbfd-130">Загружается маршрутизатор Blazor, и компонент Razor `Main` преобразовывается для просмотра.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-130">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="ebbfd-131">На странице Main возможность выбора ссылки на компонент `About` доступна на клиентском компьютере, так как маршрутизатор Blazor не разрешает браузеру сделать запрос в Интернете к `www.contoso.com` для `About` и сам обрабатывает отображенный компонент `About`.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-131">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="ebbfd-132">Все запросы внутренних конечных точек *в пределах приложения Blazor WebAssembly* будут работать так же: Запросы не активируют браузерные запросы к ресурсам, размещенным на сервере в Интернете.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-132">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="ebbfd-133">Маршрутизатор обрабатывает запросы внутренним образом.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-133">The router handles the requests internally.</span></span>

<span data-ttu-id="ebbfd-134">Если запрос `www.contoso.com/About` сделан с помощью адресной строки браузера, он завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-134">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="ebbfd-135">Такой ресурс не существует на интернет-узле приложения, поэтому возвращается ответ *404 — Not Found* (не найдено).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-135">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="ebbfd-136">Поскольку браузеры выполняют запросы к интернет-узлам для страниц на стороне клиента, веб-серверам и службам размещения необходимо переопределять все запросы к ресурсам, физически находящимся не на сервере, в случае страницы *index.html*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-136">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="ebbfd-137">Когда *index.html* возвращается, маршрутизатор Blazor приложения берет на себя управление и отвечает необходимым ресурсом.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-137">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="ebbfd-138">При развертывании на сервере IIS можно использовать модуль переопределения URL-адресов с опубликованным файлом приложения *web.config*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-138">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="ebbfd-139">Дополнительные сведения см. в описании [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-139">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="ebbfd-140">Размещенное развертывание с использованием ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebbfd-140">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="ebbfd-141">*Размещенное развертывание*  обслуживает приложение Blazor WebAssembly для браузеров из [приложения ASP.NET Core](xref:index), выполняющегося на веб-сервере.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-141">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="ebbfd-142">Клиентское приложение Blazor WebAssembly публикуется в папку */bin/Release/{Целевая_платформа}/publish/wwwroot* серверного приложения вместе со всеми прочими статическими веб-ресурсами серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-142">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="ebbfd-143">Оба приложения развертываются вместе.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-143">The two apps are deployed together.</span></span> <span data-ttu-id="ebbfd-144">Веб-сервер, позволяющий размещать приложения ASP.NET Core, является обязательным.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-144">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="ebbfd-145">Для размещаемого развертывания Visual Studio включает шаблон проекта **приложения Blazor WebAssembly** (шаблон `blazorwasm` при использовании команды [dotnet new](/dotnet/core/tools/dotnet-new)) с выбранным параметром **Hosted** (`-ho|--hosted` при использовании команды `dotnet new`).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-145">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="ebbfd-146">Дополнительные сведения о размещении приложения ASP.NET Core и его развертывании см. в разделе <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-146">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="ebbfd-147">Подробнее о развертывании в службе приложений Azure см. в <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-147">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="ebbfd-148">Автономное развертывание</span><span class="sxs-lookup"><span data-stu-id="ebbfd-148">Standalone deployment</span></span>

<span data-ttu-id="ebbfd-149">*Автономное развертывание* обрабатывает приложение Blazor WebAssembly как набор статических файлов, которые будут запрашиваться непосредственно клиентами.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-149">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="ebbfd-150">Любой статический файловый сервер способен обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-150">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="ebbfd-151">Ресурсы автономного развертывания публикуются в папку *bin/Release/{Целевая_платформа}publish/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-151">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="ebbfd-152">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="ebbfd-152">Azure App Service</span></span>

<span data-ttu-id="ebbfd-153">Приложения Blazor WebAssembly можно развернуть в службах приложений Azure в Windows. При этом приложение размещается в службах [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-153">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="ebbfd-154">Развертывание автономного приложения Blazor WebAssembly в Службе приложений Azure для Linux в настоящее время не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-154">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="ebbfd-155">Сейчас образ сервера Linux для размещения приложения недоступен.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-155">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="ebbfd-156">Ведется работа над реализацией этого сценария.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-156">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="ebbfd-157">IIS</span><span class="sxs-lookup"><span data-stu-id="ebbfd-157">IIS</span></span>

<span data-ttu-id="ebbfd-158">IIS можно использовать как полнофункциональный статический файловый сервер для приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-158">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="ebbfd-159">Чтобы настроить IIS для размещения Blazor, перейдите к разделу [Развертывание статического веб-сайта на IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-159">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="ebbfd-160">Опубликованные ресурсы создаются в папке */bin/Release/{целевая_платформа}/publish*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-160">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="ebbfd-161">Разместить содержимое папки *publish* на веб-сервере или в службе размещения.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-161">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="ebbfd-162">web.config.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-162">web.config</span></span>

<span data-ttu-id="ebbfd-163">При публикации проекта Blazor создается файл *web.config* со следующей конфигурацией IIS:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-163">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="ebbfd-164">Типы MIME заданы для следующих расширений файлов:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-164">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="ebbfd-165">*.dll*: `application/octet-stream`;</span><span class="sxs-lookup"><span data-stu-id="ebbfd-165">*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="ebbfd-166">*.json*: `application/json`;</span><span class="sxs-lookup"><span data-stu-id="ebbfd-166">*.json*: `application/json`</span></span>
  * <span data-ttu-id="ebbfd-167">*.wasm*: `application/wasm`;</span><span class="sxs-lookup"><span data-stu-id="ebbfd-167">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="ebbfd-168">*.woff*: `application/font-woff`;</span><span class="sxs-lookup"><span data-stu-id="ebbfd-168">*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="ebbfd-169">*.woff2*: `application/font-woff`.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-169">*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="ebbfd-170">Сжатие HTTP включено для следующих типов MIME:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-170">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="ebbfd-171">Задаются правила модуля переопределения URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-171">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="ebbfd-172">Предоставление подкаталога для размещения статических ресурсов приложения (*wwwroot/{Запрошенный_путь}* ).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-172">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="ebbfd-173">Создание резервной маршрутизации для одностраничного приложения, которая позволяет перенаправлять запросы, относящиеся к нефайловым ресурсам, к документу приложения по умолчанию в папке статических ресурсов (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-173">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="ebbfd-174">Использование пользовательского файла web.config</span><span class="sxs-lookup"><span data-stu-id="ebbfd-174">Use a custom web.config</span></span>

<span data-ttu-id="ebbfd-175">Чтобы применить пользовательский файл *web.config*, поместите пользовательский файл *web.config* в корневую папку проекта и опубликуйте проект.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-175">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="ebbfd-176">Установка модуля переопределения URL-адресов</span><span class="sxs-lookup"><span data-stu-id="ebbfd-176">Install the URL Rewrite Module</span></span>

<span data-ttu-id="ebbfd-177">[Модуль переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite) нужен, чтобы переопределять URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-177">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="ebbfd-178">Модуль не устанавливается по умолчанию и недоступен для установки как компонент службы роли веб-сервера (IIS).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-178">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="ebbfd-179">Модуль необходимо скачать с веб-сайта IIS.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-179">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="ebbfd-180">Для его установки используйте установщик веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-180">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="ebbfd-181">Локально перейдите на [страницу скачивания модуля переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-181">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="ebbfd-182">В англоязычной версии выберите **WebPI** для скачивания установщика веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-182">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="ebbfd-183">Для других языков выберите подходящую архитектуру сервера (x86 или x64) для скачивания установщика.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-183">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="ebbfd-184">Скопируйте установщик на сервер.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-184">Copy the installer to the server.</span></span> <span data-ttu-id="ebbfd-185">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-185">Run the installer.</span></span> <span data-ttu-id="ebbfd-186">Нажмите кнопку **Установить** и примите условия лицензионного соглашения.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-186">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="ebbfd-187">Перезагрузка сервера после завершения установки не требуется.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-187">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="ebbfd-188">Настройка веб-сайта</span><span class="sxs-lookup"><span data-stu-id="ebbfd-188">Configure the website</span></span>

<span data-ttu-id="ebbfd-189">Установите для веб сайта **физический путь** к папке приложения.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-189">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="ebbfd-190">Папка содержит:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-190">The folder contains:</span></span>

* <span data-ttu-id="ebbfd-191">Файл *web.config*, который используется службами IIS для настройки веб-сайта, включая необходимые правила перенаправления и типы содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-191">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="ebbfd-192">Папку статических ресурсов приложения.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-192">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="ebbfd-193">Размещение в качестве дочернего приложения IIS</span><span class="sxs-lookup"><span data-stu-id="ebbfd-193">Host as an IIS sub-app</span></span>

<span data-ttu-id="ebbfd-194">Если автономное приложение размещено как дочернее приложение IIS, выполните одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-194">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="ebbfd-195">Отключите наследуемый обработчик модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-195">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="ebbfd-196">Удалите обработчик в опубликованном файле *web.config* приложения Blazor, добавив раздел `<handlers>` в файл:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-196">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="ebbfd-197">Отключите наследование раздела `<system.webServer>` от корневого (родительского) приложения, используя `<location>` со значением `false` для `inheritInChildApplications`:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-197">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="ebbfd-198">Удаление обработчика или отключение наследования выполняется вместе с [настройкой базового пути приложения](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-198">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="ebbfd-199">Задайте базовый путь приложения в файле *index.html* приложения как псевдоним IIS, используемый при настройке дочернего приложения в IIS.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-199">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="ebbfd-200">Алгоритмы сжатия Brotli и Gzip</span><span class="sxs-lookup"><span data-stu-id="ebbfd-200">Brotli and Gzip compression</span></span>

<span data-ttu-id="ebbfd-201">С помощью файла *web.config* можно настроить IIS для обслуживания ресурсов Blazor, сжатых с помощью алгоритмов Brotli или gzip.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-201">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="ebbfd-202">Пример конфигурации см. в файле [web.config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-202">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="ebbfd-203">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="ebbfd-203">Troubleshooting</span></span>

<span data-ttu-id="ebbfd-204">Если получено сообщение *500 — Internal Server Error* (внутренняя ошибка сервера), а диспетчер служб IIS возвращает ошибки при попытке получить доступ к конфигурации веб сайта, убедитесь, что установлен модуль переопределения URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-204">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="ebbfd-205">Если модуль не установлен, IIS не может проанализировать файл *web.config*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-205">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="ebbfd-206">Это предотвращает загрузку конфигурации веб сайта диспетчером служб IIS и передачу статических файлов Blazor c веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-206">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="ebbfd-207">Дополнительные сведения об устранении неполадок развертывания в службах IIS см. в разделе <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-207">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="ebbfd-208">Хранилище Azure</span><span class="sxs-lookup"><span data-stu-id="ebbfd-208">Azure Storage</span></span>

<span data-ttu-id="ebbfd-209">Размещение статических файлов [службы хранилища Azure](/azure/storage/) предусматривает размещение бессерверного приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-209">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="ebbfd-210">Поддерживаются пользовательские доменные имена, сеть доставки содержимого Azure (CDN) и HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-210">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="ebbfd-211">Если служба BLOB-объектов настроена для размещения статических веб-сайтов в учетной записи хранения:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-211">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="ebbfd-212">Задайте для параметра **Имя документа индекса** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-212">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="ebbfd-213">Задайте для параметра **Путь к документу с ошибкой** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-213">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="ebbfd-214">Компоненты Razor и другие конечные точки, не являющиеся файлами, не имеют физических путей в статическом содержимом, хранящемся в службе BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-214">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="ebbfd-215">При получении запроса для одного из этих ресурсов, который должен обрабатываться маршрутизатором Blazor, ошибка *404 - Объект не найден*, создаваемая службой BLOB-объектов, перенаправляет запрос, используя **путь к документу с ошибкой**.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-215">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="ebbfd-216">При этом возвращается большой двоичный объект *index.html*, и маршрутизатор Blazor загружает и обрабатывает путь.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-216">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="ebbfd-217">См. подробнее о [размещении статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-217">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="ebbfd-218">Nginx</span><span class="sxs-lookup"><span data-stu-id="ebbfd-218">Nginx</span></span>

<span data-ttu-id="ebbfd-219">Следующий файл *nginx.conf* упрощен для демонстрации того, как настроить Nginx для отправки файла *Index.html*, когда не удается найти соответствующий файл на диске.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-219">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="ebbfd-220">Дополнительные сведения о конфигурации веб-сервера Nginx в рабочей среде см. в разделе [Создание файлов конфигурации NGINX Plus и NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-220">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="ebbfd-221">Nginx в Docker</span><span class="sxs-lookup"><span data-stu-id="ebbfd-221">Nginx in Docker</span></span>

<span data-ttu-id="ebbfd-222">Чтобы разместить Blazor в Docker с помощью Nginx, настройте в Dockerfile образ с Nginx на основе Alpine.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-222">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="ebbfd-223">Измените Dockerfile, скопировав файл *nginx.config* в контейнер.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-223">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="ebbfd-224">Добавьте одну строку в Dockerfile, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-224">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="ebbfd-225">Apache</span><span class="sxs-lookup"><span data-stu-id="ebbfd-225">Apache</span></span>

<span data-ttu-id="ebbfd-226">Чтобы развернуть приложение Blazor WebAssembly в CentOS 7 или более поздней версии, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-226">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="ebbfd-227">Создайте файл конфигурации Apache.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-227">Create the Apache configuration file.</span></span> <span data-ttu-id="ebbfd-228">В следующем примере показан упрощенный файл конфигурации (*blazorapp.config*):</span><span class="sxs-lookup"><span data-stu-id="ebbfd-228">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="ebbfd-229">Поместите файл конфигурации Apache в каталог `/etc/httpd/conf.d/`. Это каталог конфигурации Apache по умолчанию в CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-229">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="ebbfd-230">Поместите файлы приложения в каталог `/var/www/blazorapp` (расположение, указанное в параметре `DocumentRoot` в файле конфигурации).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-230">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="ebbfd-231">Перезапустите службу Apache.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-231">Restart the Apache service.</span></span>

<span data-ttu-id="ebbfd-232">Дополнительные сведения см. в разделах [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) и [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-232">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="ebbfd-233">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="ebbfd-233">GitHub Pages</span></span>

<span data-ttu-id="ebbfd-234">Чтобы обеспечить переопределение URL-адресов, добавьте файл *404.html* со сценарием, который производит перенаправление на страницу *index.html*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-234">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="ebbfd-235">Пример реализации, предоставленный сообществом, см. в разделе [Одностраничные приложения для страниц GitHub](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages на сайте GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-235">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="ebbfd-236">Пример с использованием подхода сообщества можно увидеть в разделе [blazor-demo/blazor-demo.github.io на сайте GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([активный сайт](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-236">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="ebbfd-237">При использовании сайта проекта вместо сайта организации следует добавить или обновить тег `<base>` в файле *index.html*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-237">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="ebbfd-238">Задайте для имени репозитория GitHub значение атрибута `href` с косой чертой в конце (например, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-238">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="ebbfd-239">Значения конфигурации узла</span><span class="sxs-lookup"><span data-stu-id="ebbfd-239">Host configuration values</span></span>

<span data-ttu-id="ebbfd-240">[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) могут принимать следующие значения конфигурации узла в качестве аргументов командной строки во время выполнения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-240">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="ebbfd-241">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="ebbfd-241">Content root</span></span>

<span data-ttu-id="ebbfd-242">Аргумент `--contentroot` задает абсолютный путь к каталогу, где находятся файлы содержимого приложения ([корневой каталог содержимого](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-242">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="ebbfd-243">В следующих примерах `/content-root-path` — это путь к корневому каталогу содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-243">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="ebbfd-244">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-244">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ebbfd-245">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-245">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="ebbfd-246">Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-246">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ebbfd-247">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-247">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="ebbfd-248">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-248">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ebbfd-249">Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-249">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="ebbfd-250">Базовый путь</span><span class="sxs-lookup"><span data-stu-id="ebbfd-250">Path base</span></span>

<span data-ttu-id="ebbfd-251">Аргумент `--pathbase` задает базовый путь приложения для локального выполнения с некорневым относительным путем URL (в `<base>` тег `href` задан с путем, отличным от `/` для промежуточной и рабочей сред).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-251">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="ebbfd-252">В следующих примерах `/relative-URL-path` — это базовый путь приложения.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-252">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="ebbfd-253">Дополнительные сведения см. в описании [базового пути приложения](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-253">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ebbfd-254">В отличие от пути, заданного через `href` в теге `<base>`, не ставьте косую черту (`/`) при передаче значения аргумента `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-254">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="ebbfd-255">Если основной путь приложения задан в теге `<base>` как `<base href="/CoolApp/">` (включая косую черту в конце), передайте значение аргумента командной строки как `--pathbase=/CoolApp` (без косой черты в конце).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-255">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="ebbfd-256">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-256">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ebbfd-257">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-257">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="ebbfd-258">Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-258">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ebbfd-259">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-259">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="ebbfd-260">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-260">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ebbfd-261">Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-261">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="ebbfd-262">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="ebbfd-262">URLs</span></span>

<span data-ttu-id="ebbfd-263">Аргумент `--urls` задает IP-адреса или адреса узлов с портами и протоколами, по которым ожидаются запросы.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-263">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="ebbfd-264">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-264">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ebbfd-265">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-265">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="ebbfd-266">Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-266">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ebbfd-267">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-267">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="ebbfd-268">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-268">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ebbfd-269">Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-269">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="ebbfd-270">Настройка компоновщика</span><span class="sxs-lookup"><span data-stu-id="ebbfd-270">Configure the Linker</span></span>

Blazor<span data-ttu-id="ebbfd-271"> выполняет компоновку для промежуточного языка (IL) в каждой сборке выпуска, чтобы удалить ненужный промежуточный язык из выходных сборок.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-271"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="ebbfd-272">Для получения дополнительной информации см. <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-272">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="ebbfd-273">Загрузка пользовательских ресурсов загрузки</span><span class="sxs-lookup"><span data-stu-id="ebbfd-273">Custom boot resource loading</span></span>

<span data-ttu-id="ebbfd-274">Приложение Blazor WebAssembly можно инициализировать с помощью функции `loadBootResource`, чтобы переопределить встроенный механизм загрузки ресурсов загрузки.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-274">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="ebbfd-275">Используйте `loadBootResource` в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-275">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="ebbfd-276">Разрешить пользователям загружать статические ресурсы, такие как данные о часовом поясе или *dotnet.wasm*, из сети CDN.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-276">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="ebbfd-277">Загрузить сжатые сборки с помощью HTTP-запроса и распаковать их на клиенте для узлов, которые не поддерживают получение сжатого содержимого с сервера.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-277">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="ebbfd-278">Создать псевдоним ресурсов, выбрав другое имя путем перенаправления каждого запроса `fetch` на новое имя.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-278">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="ebbfd-279">Параметры `loadBootResource` приведены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-279">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="ebbfd-280">Параметр</span><span class="sxs-lookup"><span data-stu-id="ebbfd-280">Parameter</span></span>    | <span data-ttu-id="ebbfd-281">Описание</span><span class="sxs-lookup"><span data-stu-id="ebbfd-281">Description</span></span> |
| ---
<span data-ttu-id="ebbfd-282">title: Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core автор: описание: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и GitHub Pages.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-282">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ebbfd-283">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-283">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebbfd-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-284">'Blazor'</span></span>
- <span data-ttu-id="ebbfd-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-285">'Identity'</span></span>
- <span data-ttu-id="ebbfd-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebbfd-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-287">'Razor'</span></span>
- <span data-ttu-id="ebbfd-288">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="ebbfd-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebbfd-289">title: Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core автор: описание: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и GitHub Pages.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-289">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ebbfd-290">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-290">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebbfd-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-291">'Blazor'</span></span>
- <span data-ttu-id="ebbfd-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-292">'Identity'</span></span>
- <span data-ttu-id="ebbfd-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebbfd-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-294">'Razor'</span></span>
- <span data-ttu-id="ebbfd-295">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="ebbfd-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebbfd-296">title: Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core автор: описание: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и GitHub Pages.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-296">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ebbfd-297">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-297">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebbfd-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-298">'Blazor'</span></span>
- <span data-ttu-id="ebbfd-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-299">'Identity'</span></span>
- <span data-ttu-id="ebbfd-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebbfd-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-301">'Razor'</span></span>
- <span data-ttu-id="ebbfd-302">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="ebbfd-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebbfd-303">title: Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core автор: описание: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и GitHub Pages.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-303">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ebbfd-304">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-304">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebbfd-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-305">'Blazor'</span></span>
- <span data-ttu-id="ebbfd-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-306">'Identity'</span></span>
- <span data-ttu-id="ebbfd-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebbfd-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-308">'Razor'</span></span>
- <span data-ttu-id="ebbfd-309">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="ebbfd-309">'SignalR' uid:</span></span> 

<span data-ttu-id="ebbfd-310">------ | --- название: Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core автор: описание: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и GitHub Pages.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-310">------ | --- title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ebbfd-311">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-311">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebbfd-312">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-312">'Blazor'</span></span>
- <span data-ttu-id="ebbfd-313">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-313">'Identity'</span></span>
- <span data-ttu-id="ebbfd-314">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-314">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebbfd-315">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-315">'Razor'</span></span>
- <span data-ttu-id="ebbfd-316">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="ebbfd-316">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebbfd-317">title: Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core автор: описание: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и GitHub Pages.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-317">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ebbfd-318">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-318">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebbfd-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-319">'Blazor'</span></span>
- <span data-ttu-id="ebbfd-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-320">'Identity'</span></span>
- <span data-ttu-id="ebbfd-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebbfd-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-322">'Razor'</span></span>
- <span data-ttu-id="ebbfd-323">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="ebbfd-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebbfd-324">title: Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core автор: описание: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и GitHub Pages.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-324">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ebbfd-325">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-325">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebbfd-326">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-326">'Blazor'</span></span>
- <span data-ttu-id="ebbfd-327">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-327">'Identity'</span></span>
- <span data-ttu-id="ebbfd-328">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-328">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebbfd-329">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebbfd-329">'Razor'</span></span>
- <span data-ttu-id="ebbfd-330">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="ebbfd-330">'SignalR' uid:</span></span> 

<span data-ttu-id="ebbfd-331">------ | | `type`       | Тип ресурса.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-331">------ | | `type`       | The type of the resource.</span></span> <span data-ttu-id="ebbfd-332">Допустимые типы: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` | | `name`       | Имя ресурса.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-332">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` | | `name`       | The name of the resource.</span></span> <span data-ttu-id="ebbfd-333">| | `defaultUri` | Относительный или абсолютный URI ресурса.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-333">| | `defaultUri` | The relative or absolute URI of the resource.</span></span> <span data-ttu-id="ebbfd-334">| | `integrity`  | Строка целостности, представляющая ожидаемое содержимое в ответе.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-334">| | `integrity`  | The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="ebbfd-335">`loadBootResource` возвращает любой из следующих элементов для переопределения процесса загрузки.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-335">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="ebbfd-336">Строка URI.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-336">URI string.</span></span> <span data-ttu-id="ebbfd-337">В следующем примере (*wwwroot/index.html*) следующие файлы обслуживаются из сети CDN в `https://my-awesome-cdn.com/`.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-337">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="ebbfd-338">*dotnet.\*.js*</span><span class="sxs-lookup"><span data-stu-id="ebbfd-338">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="ebbfd-339">*dotnet.wasm*</span><span class="sxs-lookup"><span data-stu-id="ebbfd-339">*dotnet.wasm*</span></span>
  * <span data-ttu-id="ebbfd-340">Данные часового пояса</span><span class="sxs-lookup"><span data-stu-id="ebbfd-340">Timezone data</span></span>

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

* <span data-ttu-id="ebbfd-341">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-341">`Promise<Response>`.</span></span> <span data-ttu-id="ebbfd-342">Передайте параметр `integrity` в заголовке для сохранения поведения проверки целостности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-342">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="ebbfd-343">В следующем примере (*wwwroot/index.html*) в исходящие запросы добавляется пользовательский заголовок HTTP и передается параметр `integrity` в вызов `fetch`.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-343">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="ebbfd-344">`null`/`undefined`, что приводит к поведению загрузки по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-344">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="ebbfd-345">Внешние источники должны возвращать необходимые заголовки CORS для браузеров, чтобы разрешить загрузку ресурсов между источниками.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-345">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="ebbfd-346">По умолчанию CDN обычно предоставляют необходимые заголовки.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-346">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="ebbfd-347">Необходимо указать только типы для пользовательских поведений.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-347">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="ebbfd-348">Типы, не указанные в `loadBootResource`, загружаются платформой в соответствии с поведением при загрузке по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-348">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="ebbfd-349">Изменение расширения DLL-файлов.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-349">Change the filename extension of DLL files</span></span>

<span data-ttu-id="ebbfd-350">Если вам необходимо изменить расширения опубликованных *DLL*-файлов приложения, следуйте указаниям в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-350">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="ebbfd-351">После публикации приложения используйте скрипт оболочки или конвейер сборки DevOps для переименования *DLL*-файлов для использования другого расширения файла.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-351">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="ebbfd-352">Указывайте на *DLL*-файлы в каталоге *wwwroot* опубликованных выходных данных приложения (например, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-352">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="ebbfd-353">В следующих примерах *DLL*-файлы переименованы для использования расширения файла *BIN*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-353">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="ebbfd-354">Windows:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-354">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="ebbfd-355">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-355">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="ebbfd-356">В Linux или macOS.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-356">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="ebbfd-357">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-357">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="ebbfd-358">Чтобы использовать расширение файла, отличное от *BIN*, замените *BIN* в предыдущих командах.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-358">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="ebbfd-359">Для сжатых файлов *blazor.boot.json.gz* и *blazor.boot.json.br* используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-359">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="ebbfd-360">Удалите сжатые файлы *blazor.boot.json.gz* и *blazor.boot.json.br*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-360">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="ebbfd-361">Сжатие при таком подходе отключается.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-361">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="ebbfd-362">Выполните повторное сжатие обновленного файла *blazor.boot.json*.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-362">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="ebbfd-363">Приведенные выше рекомендации также применимы при использовании ресурсов рабочей роли.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-363">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="ebbfd-364">Удалите или повторно распакуйте файлы *wwwroot/service-worker-assets.js.br* и *wwwroot/service-worker-assets.js.gz*,</span><span class="sxs-lookup"><span data-stu-id="ebbfd-364">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span> <span data-ttu-id="ebbfd-365">иначе проверка целостности файлов в браузере завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-365">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="ebbfd-366">В следующем примере Windows используется скрипт PowerShell, размещенный в корне проекта.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-366">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="ebbfd-367">*ChangeDLLExtensions.ps1:* .</span><span class="sxs-lookup"><span data-stu-id="ebbfd-367">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="ebbfd-368">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ebbfd-368">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="ebbfd-369">В файле проекта скрипт запускается после публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="ebbfd-369">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="ebbfd-370">Чтобы оставить отзыв, перейдите на страницу [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="ebbfd-370">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 