---
title: Размещение и развертывание ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и страниц GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 3436620123618ab32daa44c4a37057aaadb89563
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393695"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="73b7a-103">Размещение и развертывание ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="73b7a-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="73b7a-104">Авторы: [Люк Лэтем](https://github.com/guardrex) (Luke Latham), [Рэйнер Стропек](https://www.timecockpit.com) (Rainer Stropek), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth), [Бен Адамс](https://twitter.com/ben_a_adams) (Ben Adams) и [Сафиа Абдалла](https://safia.rocks) (Safia Abdalla).</span><span class="sxs-lookup"><span data-stu-id="73b7a-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="73b7a-105">При использовании [модели размещения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="73b7a-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="73b7a-106">Приложение Blazor, его зависимости и среда выполнения .NET скачиваются в браузере параллельно.</span><span class="sxs-lookup"><span data-stu-id="73b7a-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="73b7a-107">Приложение выполняется непосредственно в потоке пользовательского интерфейса браузера.</span><span class="sxs-lookup"><span data-stu-id="73b7a-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="73b7a-108">Поддерживаются следующие стратегии развертывания:</span><span class="sxs-lookup"><span data-stu-id="73b7a-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="73b7a-109">Приложение Blazor обслуживается приложением ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73b7a-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="73b7a-110">Эта стратегия описана в разделе [Размещенное развертывание с использованием ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="73b7a-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="73b7a-111">Приложение Blazor помещается на статическом веб-сервере или службе, где .NET не используется, чтобы обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="73b7a-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="73b7a-112">Эта стратегия рассматривается в разделе [Автономное развертывание](#standalone-deployment), где приводятся сведения о размещении приложения Blazor WebAssembly в качестве дочернего приложения IIS.</span><span class="sxs-lookup"><span data-stu-id="73b7a-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="73b7a-113">Сжатие</span><span class="sxs-lookup"><span data-stu-id="73b7a-113">Compression</span></span>

<span data-ttu-id="73b7a-114">При публикации приложения Blazor WebAssembly выходные данные статически сжимаются, чтобы уменьшить размер приложения и исключить издержки на сжатие среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="73b7a-115">Используются следующие алгоритмы сжатия:</span><span class="sxs-lookup"><span data-stu-id="73b7a-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="73b7a-116">[Brotli](https://tools.ietf.org/html/rfc7932) (высший уровень)</span><span class="sxs-lookup"><span data-stu-id="73b7a-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="73b7a-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="73b7a-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="73b7a-118">Для обслуживания соответствующих сжатых файлов в Blazor используется ведущий проект.</span><span class="sxs-lookup"><span data-stu-id="73b7a-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="73b7a-119">При использовании размещенного проекта ASP.NET Core ведущий проект может выполнять согласование содержимого и обслуживать статически сжатые файлы.</span><span class="sxs-lookup"><span data-stu-id="73b7a-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="73b7a-120">При размещении автономного приложения Blazor WebAssembly, возможно, потребуется реализовать дополнительные действия для обслуживания статически сжатых файлов.</span><span class="sxs-lookup"><span data-stu-id="73b7a-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="73b7a-121">Сведения о конфигурации сжатия `web.config` IIS см. в статье [IIS: алгоритмы сжатия Brotli и Gzip](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="73b7a-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="73b7a-122">При размещении в статических решениях размещения, которые не поддерживают согласование содержимого статически сжатых файлов, например на страницах GitHub, рассмотрите возможность настройки приложения для извлечения и декодирования сжатых файлов Brotli:</span><span class="sxs-lookup"><span data-stu-id="73b7a-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="73b7a-123">Получите декодер JavaScript Brotli из [репозитория GitHub google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="73b7a-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="73b7a-124">По состоянию на сентябрь 2020 г. файл декодера называется `decode.js` и находится в [папке `js`](https://github.com/google/brotli/tree/master/js) репозитория.</span><span class="sxs-lookup"><span data-stu-id="73b7a-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="73b7a-125">Регрессия содержится в сокращенной версии скрипта `decode.js` (`decode.min.js`) в [репозитории GitHub google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="73b7a-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="73b7a-126">Либо сократите скрипт самостоятельно, либо используйте [пакет npm](https://www.npmjs.com/package/brotli), пока проблема [Параметр Window.BrotliDecode не задан в decode.min.js (google/brotli № 844)](https://github.com/google/brotli/issues/844) не будет устранена.</span><span class="sxs-lookup"><span data-stu-id="73b7a-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="73b7a-127">В примере кода в этом разделе используется **не сокращенная** версия скрипта.</span><span class="sxs-lookup"><span data-stu-id="73b7a-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="73b7a-128">обновите приложение для использования декодера.</span><span class="sxs-lookup"><span data-stu-id="73b7a-128">Update the app to use the decoder.</span></span> <span data-ttu-id="73b7a-129">Измените разметку в закрывающем теге `<body>` в `wwwroot/index.html` следующим образом.</span><span class="sxs-lookup"><span data-stu-id="73b7a-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
<span data-ttu-id="73b7a-130">Чтобы отключить сжатие, добавьте свойство `BlazorEnableCompression` MSBuild в файл проекта приложения и задайте для него значение `false`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="73b7a-131">Свойство `BlazorEnableCompression` можно передать в команду [`dotnet publish`](/dotnet/core/tools/dotnet-publish) с помощью следующего синтаксиса в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="73b7a-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="73b7a-132">Переопределение URL-адресов для маршрутизации</span><span class="sxs-lookup"><span data-stu-id="73b7a-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="73b7a-133">Маршрутизация запросов к компонентам страниц в приложении Blazor WebAssembly не настолько проста, как маршрутизация запросов к размещенному приложению Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="73b7a-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="73b7a-134">Рассмотрим сценарий с приложением Blazor WebAssembly с двумя компонентами:</span><span class="sxs-lookup"><span data-stu-id="73b7a-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="73b7a-135">`Main.razor`. загружается в корне приложения и содержит ссылку на компонент `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="73b7a-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="73b7a-136">`About.razor`: компонент `About`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="73b7a-137">При запросе документа приложения по умолчанию в адресной строке браузера (например, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="73b7a-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="73b7a-138">Браузер отправляет запрос.</span><span class="sxs-lookup"><span data-stu-id="73b7a-138">The browser makes a request.</span></span>
1. <span data-ttu-id="73b7a-139">Возвращается страница по умолчанию; обычно это `index.html`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="73b7a-140">Страница `index.html` обеспечивает начальную загрузку приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="73b7a-141">Загружается маршрутизатор Blazor, и компонент Razor `Main` преобразовывается для просмотра.</span><span class="sxs-lookup"><span data-stu-id="73b7a-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="73b7a-142">На странице Main возможность выбора ссылки на компонент `About` доступна на клиентском компьютере, так как маршрутизатор Blazor не разрешает браузеру сделать запрос в Интернете к `www.contoso.com` для `About` и сам обрабатывает отображенный компонент `About`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="73b7a-143">Все запросы внутренних конечных точек *в пределах приложения Blazor WebAssembly* будут работать так же: Запросы не активируют браузерные запросы к ресурсам, размещенным на сервере в Интернете.</span><span class="sxs-lookup"><span data-stu-id="73b7a-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="73b7a-144">Маршрутизатор обрабатывает запросы внутренним образом.</span><span class="sxs-lookup"><span data-stu-id="73b7a-144">The router handles the requests internally.</span></span>

<span data-ttu-id="73b7a-145">Если запрос `www.contoso.com/About` сделан с помощью адресной строки браузера, он завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="73b7a-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="73b7a-146">Такой ресурс не существует на интернет-узле приложения, поэтому возвращается ответ *404 — Not Found* (не найдено).</span><span class="sxs-lookup"><span data-stu-id="73b7a-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="73b7a-147">Так как браузеры выполняют запросы к интернет-узлам для страниц на стороне клиента, веб-серверам и службам размещения необходимо переопределять все запросы к ресурсам, физически находящимся не на сервере, в случае страницы `index.html`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="73b7a-148">Когда `index.html` возвращается, маршрутизатор Blazor приложения берет на себя управление и отвечает необходимым ресурсом.</span><span class="sxs-lookup"><span data-stu-id="73b7a-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="73b7a-149">При развертывании на сервере IIS можно использовать модуль переопределения URL-адресов с опубликованным файлом `web.config` приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="73b7a-150">Дополнительные сведения см. в описании [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="73b7a-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="73b7a-151">Размещенное развертывание с использованием ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73b7a-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="73b7a-152">*Размещенное развертывание* обслуживает приложение Blazor WebAssembly для браузеров из [приложения ASP.NET Core](xref:index), выполняющегося на веб-сервере.</span><span class="sxs-lookup"><span data-stu-id="73b7a-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="73b7a-153">Клиентское приложение Blazor WebAssembly будет опубликовано в папку `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` серверного приложения вместе со статическими веб-ресурсами серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="73b7a-154">Оба приложения развертываются вместе.</span><span class="sxs-lookup"><span data-stu-id="73b7a-154">The two apps are deployed together.</span></span> <span data-ttu-id="73b7a-155">Веб-сервер, позволяющий размещать приложения ASP.NET Core, является обязательным.</span><span class="sxs-lookup"><span data-stu-id="73b7a-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="73b7a-156">Для размещаемого развертывания Visual Studio включает шаблон проекта **Приложение Blazor WebAssembly** (шаблон `blazorwasm` при использовании команды [`dotnet new`](/dotnet/core/tools/dotnet-new)) с выбранным параметром **`Hosted`** (`-ho|--hosted` при использовании команды `dotnet new`).</span><span class="sxs-lookup"><span data-stu-id="73b7a-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="73b7a-157">Дополнительные сведения о размещении приложения ASP.NET Core и его развертывании см. в разделе <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="73b7a-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="73b7a-158">Подробнее о развертывании в службе приложений Azure см. в <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="73b7a-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="73b7a-159">Размещенное развертывание с несколькими приложениями Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="73b7a-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="73b7a-160">Конфигурация приложения</span><span class="sxs-lookup"><span data-stu-id="73b7a-160">App configuration</span></span>

<span data-ttu-id="73b7a-161">Чтобы настроить размещенное решение Blazor для обслуживания нескольких приложений Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="73b7a-161">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="73b7a-162">Используйте существующее размещенное решение Blazor или создайте новое решение из размещенного шаблона проекта Blazor.</span><span class="sxs-lookup"><span data-stu-id="73b7a-162">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="73b7a-163">В файле проекта клиентского приложения добавьте свойство `<StaticWebAssetBasePath>` в `<PropertyGroup>` со значением `FirstApp`, чтобы задать базовый путь для статических ресурсов проекта.</span><span class="sxs-lookup"><span data-stu-id="73b7a-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="73b7a-164">Добавьте в решение второе клиентское приложение:</span><span class="sxs-lookup"><span data-stu-id="73b7a-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="73b7a-165">Добавьте папку с именем `SecondClient` в папку решения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="73b7a-166">Создайте приложение Blazor WebAssembly с именем `SecondBlazorApp.Client` в папке `SecondClient` из шаблона проекта Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="73b7a-166">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="73b7a-167">В файле проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="73b7a-167">In the app's project file:</span></span>

    * <span data-ttu-id="73b7a-168">Добавьте свойство `<StaticWebAssetBasePath>` в `<PropertyGroup>` со значением `SecondApp`:</span><span class="sxs-lookup"><span data-stu-id="73b7a-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="73b7a-169">Добавьте ссылку на проект в проект `Shared`:</span><span class="sxs-lookup"><span data-stu-id="73b7a-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="73b7a-170">Заполнитель `{SOLUTION NAME}` — это имя решения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="73b7a-171">В файле проекта серверного приложения создайте ссылку на проект для добавленного клиентского приложения:</span><span class="sxs-lookup"><span data-stu-id="73b7a-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="73b7a-172">В файле серверного приложения `Properties/launchSettings.json` настройте `applicationUrl` профиля Kestrel (`{SOLUTION NAME}.Server`), чтобы получить доступ к клиентским приложениям через порты 5001 и 5002:</span><span class="sxs-lookup"><span data-stu-id="73b7a-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="73b7a-173">В методе `Startup.Configure` серверного приложения (`Startup.cs`) удалите эти строки, которые появляются после вызова <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span><span class="sxs-lookup"><span data-stu-id="73b7a-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="73b7a-174">Добавьте ПО промежуточного слоя, которое сопоставляет запросы с клиентскими приложениями.</span><span class="sxs-lookup"><span data-stu-id="73b7a-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="73b7a-175">В следующем примере выполняется настройка ПО промежуточного слоя для запуска в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="73b7a-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="73b7a-176">Порт запроса — 5001 для исходного клиентского приложения или 5002 для добавленного клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="73b7a-177">Узел запроса — `firstapp.com` для исходного клиентского приложения или `secondapp.com` для добавленного клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="73b7a-178">В примере, приведенном в этом разделе, требуется дополнительная настройка для:</span><span class="sxs-lookup"><span data-stu-id="73b7a-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="73b7a-179">Доступа к приложениям в примерах доменов узла `firstapp.com` и `secondapp.com`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="73b7a-180">Сертификатов для клиентских приложений, чтобы включить средства безопасности TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="73b7a-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="73b7a-181">Описание полной необходимой конфигурации выходит за рамки этой статьи и зависит от того, как размещается решение.</span><span class="sxs-lookup"><span data-stu-id="73b7a-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="73b7a-182">Дополнительные сведения см. в [статьях о размещении и развертывании](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="73b7a-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="73b7a-183">Поместите следующий код, где строки были удалены ранее:</span><span class="sxs-lookup"><span data-stu-id="73b7a-183">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="73b7a-184">В контроллере прогноза погоды серверного приложения (`Controllers/WeatherForecastController.cs`) замените существующий маршрут (`[Route("[controller]")]`) к `WeatherForecastController` следующими маршрутами:</span><span class="sxs-lookup"><span data-stu-id="73b7a-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="73b7a-185">ПО промежуточного слоя, добавленное в метод `Startup.Configure` серверного приложения ранее, изменяет входящие запросы к `/WeatherForecast` на `/FirstApp/WeatherForecast` или `/SecondApp/WeatherForecast` в зависимости от порта (5001/5002) или домена (`firstapp.com`/`secondapp.com`).</span><span class="sxs-lookup"><span data-stu-id="73b7a-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="73b7a-186">Предыдущие маршруты контроллеров необходимы для возврата данных о погоде из серверного приложения в клиентские приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="73b7a-187">Статические ресурсы и библиотеки классов</span><span class="sxs-lookup"><span data-stu-id="73b7a-187">Static assets and class libraries</span></span>

<span data-ttu-id="73b7a-188">Для статических ресурсов используйте следующие подходы.</span><span class="sxs-lookup"><span data-stu-id="73b7a-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="73b7a-189">Если ресурс находится в папке `wwwroot` клиентского приложения, укажите пути к ним обычным образом:</span><span class="sxs-lookup"><span data-stu-id="73b7a-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="73b7a-190">Если ресурс находится в папке `wwwroot` [библиотеки классов (RCL) Razor](xref:blazor/components/class-libraries), ссылайтесь на статический ресурс в клиентском приложении, следуя инструкциям в статье об [RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="73b7a-190">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="73b7a-191">На компоненты, предоставляемые клиентскому приложению с помощью библиотеки классов, ссылаются обычным образом.</span><span class="sxs-lookup"><span data-stu-id="73b7a-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="73b7a-192">Если какие-либо компоненты нуждаются в таблицах стилей или файлах JavaScript, используйте один из следующих подходов для получения статических ресурсов:</span><span class="sxs-lookup"><span data-stu-id="73b7a-192">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="73b7a-193">Файл `wwwroot/index.html` клиентского приложения можно связать со статическими ресурсами (`<link>`).</span><span class="sxs-lookup"><span data-stu-id="73b7a-193">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="73b7a-194">Компонент может использовать [компонент `Link`](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) платформы для получения статических ресурсов.</span><span class="sxs-lookup"><span data-stu-id="73b7a-194">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="73b7a-195">Приведенные выше подходы демонстрируются в следующих примерах.</span><span class="sxs-lookup"><span data-stu-id="73b7a-195">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="73b7a-196">На компоненты, предоставляемые клиентскому приложению с помощью библиотеки классов, ссылаются обычным образом.</span><span class="sxs-lookup"><span data-stu-id="73b7a-196">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="73b7a-197">Если какие либо компоненты нуждаются в таблицах стилей или файлах JavaScript, то файл `wwwroot/index.html` клиентского приложения должен содержать правильные ссылки на статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="73b7a-197">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="73b7a-198">Эти подходы демонстрируются в следующих примерах.</span><span class="sxs-lookup"><span data-stu-id="73b7a-198">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="73b7a-199">Добавьте следующий компонент `Jeep` в одно из клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="73b7a-199">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="73b7a-200">Компонент `Jeep` использует:</span><span class="sxs-lookup"><span data-stu-id="73b7a-200">The `Jeep` component uses:</span></span>

* <span data-ttu-id="73b7a-201">изображение из папки `wwwroot` клиентского приложения (`jeep-cj.png`);</span><span class="sxs-lookup"><span data-stu-id="73b7a-201">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="73b7a-202">изображение из папки `wwwroot` (`jeep-yj.png`) [добавленной библиотеки компонентов Razor](xref:blazor/components/class-libraries) (`JeepImage`).</span><span class="sxs-lookup"><span data-stu-id="73b7a-202">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="73b7a-203">Пример компонента (`Component1`) создается автоматически шаблоном проекта RCL при добавлении библиотеки `JeepImage` в решение.</span><span class="sxs-lookup"><span data-stu-id="73b7a-203">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="73b7a-204">**Не** публикуйте изображения автомобилей, если вы не являетесь владельцем этих изображений.</span><span class="sxs-lookup"><span data-stu-id="73b7a-204">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="73b7a-205">В противном случае вы рискуете нарушить авторские права.</span><span class="sxs-lookup"><span data-stu-id="73b7a-205">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="73b7a-206">Изображение `jeep-yj.png` из библиотеки также можно добавить в компонент `Component1` (`Component1.razor`) библиотеки.</span><span class="sxs-lookup"><span data-stu-id="73b7a-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="73b7a-207">Чтобы предоставить класс CSS `my-component` для страницы клиентского приложения, свяжите его с таблицей стилей библиотеки, используя [компонент `Link`](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) платформы:</span><span class="sxs-lookup"><span data-stu-id="73b7a-207">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="73b7a-208">Альтернативой использованию [компонента `Link`](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) является загрузка таблицы стилей из файла `wwwroot/index.html` клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-208">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="73b7a-209">Такой подход делает таблицу стилей доступной для всех компонентов в клиентском приложении:</span><span class="sxs-lookup"><span data-stu-id="73b7a-209">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="73b7a-210">Изображение `jeep-yj.png` из библиотеки также можно добавить в компонент `Component1` (`Component1.razor`) библиотеки.</span><span class="sxs-lookup"><span data-stu-id="73b7a-210">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="73b7a-211">Файл `wwwroot/index.html` клиентского приложения запрашивает таблицу стилей библиотеки со следующим добавленным тегом `<link>`:</span><span class="sxs-lookup"><span data-stu-id="73b7a-211">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="73b7a-212">Добавьте навигацию в компонент `Jeep` в компоненте `NavMenu` клиентского приложения (`Shared/NavMenu.razor`):</span><span class="sxs-lookup"><span data-stu-id="73b7a-212">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="73b7a-213">Дополнительные сведения об RCL см. здесь:</span><span class="sxs-lookup"><span data-stu-id="73b7a-213">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="73b7a-214">Автономное развертывание</span><span class="sxs-lookup"><span data-stu-id="73b7a-214">Standalone deployment</span></span>

<span data-ttu-id="73b7a-215">*Автономное развертывание* обрабатывает приложение Blazor WebAssembly как набор статических файлов, которые будут запрашиваться непосредственно клиентами.</span><span class="sxs-lookup"><span data-stu-id="73b7a-215">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="73b7a-216">Любой статический файловый сервер способен обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="73b7a-216">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="73b7a-217">Ресурсы автономного развертывания публикуются в папке `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-217">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="73b7a-218">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="73b7a-218">Azure App Service</span></span>

<span data-ttu-id="73b7a-219">Приложения Blazor WebAssembly можно развернуть в службах приложений Azure в Windows. При этом приложение размещается в службах [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="73b7a-219">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="73b7a-220">Развертывание автономного приложения Blazor WebAssembly в Службе приложений Azure для Linux в настоящее время не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="73b7a-220">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="73b7a-221">Сейчас образ сервера Linux для размещения приложения недоступен.</span><span class="sxs-lookup"><span data-stu-id="73b7a-221">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="73b7a-222">Ведется работа над реализацией этого сценария.</span><span class="sxs-lookup"><span data-stu-id="73b7a-222">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="73b7a-223">IIS</span><span class="sxs-lookup"><span data-stu-id="73b7a-223">IIS</span></span>

<span data-ttu-id="73b7a-224">IIS можно использовать как полнофункциональный статический файловый сервер для приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="73b7a-224">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="73b7a-225">Чтобы настроить IIS для размещения Blazor, перейдите к разделу [Развертывание статического веб-сайта на IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="73b7a-225">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="73b7a-226">Опубликованные ресурсы создаются в папке `/bin/Release/{TARGET FRAMEWORK}/publish`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-226">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="73b7a-227">Разместите содержимое папки `publish` на веб-сервере или в службе размещения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-227">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="73b7a-228">web.config.</span><span class="sxs-lookup"><span data-stu-id="73b7a-228">web.config</span></span>

<span data-ttu-id="73b7a-229">При публикации проекта Blazor создается файл `web.config` со следующей конфигурацией IIS:</span><span class="sxs-lookup"><span data-stu-id="73b7a-229">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="73b7a-230">Типы MIME заданы для следующих расширений файлов:</span><span class="sxs-lookup"><span data-stu-id="73b7a-230">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="73b7a-231">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="73b7a-231">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="73b7a-232">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="73b7a-232">`.json`: `application/json`</span></span>
  * <span data-ttu-id="73b7a-233">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="73b7a-233">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="73b7a-234">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="73b7a-234">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="73b7a-235">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="73b7a-235">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="73b7a-236">Сжатие HTTP включено для следующих типов MIME:</span><span class="sxs-lookup"><span data-stu-id="73b7a-236">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="73b7a-237">Задаются правила модуля переопределения URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="73b7a-237">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="73b7a-238">Предоставление подкаталога для размещения статических ресурсов приложения (`wwwroot/{PATH REQUESTED}`).</span><span class="sxs-lookup"><span data-stu-id="73b7a-238">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="73b7a-239">Создание резервной маршрутизации для одностраничного приложения, которая позволяет перенаправлять запросы, относящиеся к нефайловым ресурсам, к документу приложения по умолчанию в папке статических ресурсов (`wwwroot/index.html`).</span><span class="sxs-lookup"><span data-stu-id="73b7a-239">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="73b7a-240">Использование пользовательского файла web.config</span><span class="sxs-lookup"><span data-stu-id="73b7a-240">Use a custom web.config</span></span>

<span data-ttu-id="73b7a-241">Чтобы применить пользовательский файл `web.config`, поместите пользовательский файл `web.config` в корневую папку проекта и опубликуйте проект.</span><span class="sxs-lookup"><span data-stu-id="73b7a-241">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="73b7a-242">Установка модуля переопределения URL-адресов</span><span class="sxs-lookup"><span data-stu-id="73b7a-242">Install the URL Rewrite Module</span></span>

<span data-ttu-id="73b7a-243">[Модуль переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite) нужен, чтобы переопределять URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="73b7a-243">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="73b7a-244">Модуль не устанавливается по умолчанию и недоступен для установки как компонент службы роли веб-сервера (IIS).</span><span class="sxs-lookup"><span data-stu-id="73b7a-244">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="73b7a-245">Модуль необходимо скачать с веб-сайта IIS.</span><span class="sxs-lookup"><span data-stu-id="73b7a-245">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="73b7a-246">Для его установки используйте установщик веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="73b7a-246">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="73b7a-247">Локально перейдите на [страницу скачивания модуля переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="73b7a-247">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="73b7a-248">В англоязычной версии выберите **WebPI** для скачивания установщика веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="73b7a-248">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="73b7a-249">Для других языков выберите подходящую архитектуру сервера (x86 или x64) для скачивания установщика.</span><span class="sxs-lookup"><span data-stu-id="73b7a-249">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="73b7a-250">Скопируйте установщик на сервер.</span><span class="sxs-lookup"><span data-stu-id="73b7a-250">Copy the installer to the server.</span></span> <span data-ttu-id="73b7a-251">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="73b7a-251">Run the installer.</span></span> <span data-ttu-id="73b7a-252">Нажмите кнопку **Установить** и примите условия лицензионного соглашения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-252">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="73b7a-253">Перезагрузка сервера после завершения установки не требуется.</span><span class="sxs-lookup"><span data-stu-id="73b7a-253">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="73b7a-254">Настройка веб-сайта</span><span class="sxs-lookup"><span data-stu-id="73b7a-254">Configure the website</span></span>

<span data-ttu-id="73b7a-255">Установите для веб сайта **физический путь** к папке приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-255">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="73b7a-256">Папка содержит:</span><span class="sxs-lookup"><span data-stu-id="73b7a-256">The folder contains:</span></span>

* <span data-ttu-id="73b7a-257">Файл `web.config`, который используется службами IIS для настройки веб-сайта, включая необходимые правила перенаправления и типы содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="73b7a-257">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="73b7a-258">Папку статических ресурсов приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-258">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="73b7a-259">Размещение в качестве дочернего приложения IIS</span><span class="sxs-lookup"><span data-stu-id="73b7a-259">Host as an IIS sub-app</span></span>

<span data-ttu-id="73b7a-260">Если автономное приложение размещено как дочернее приложение IIS, выполните одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="73b7a-260">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="73b7a-261">Отключите наследуемый обработчик модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73b7a-261">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="73b7a-262">Удалите обработчик в опубликованном файле `web.config` приложения Blazor, добавив раздел `<handlers>` в файл:</span><span class="sxs-lookup"><span data-stu-id="73b7a-262">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="73b7a-263">Отключите наследование раздела `<system.webServer>` от корневого (родительского) приложения, используя `<location>` со значением `false` для `inheritInChildApplications`:</span><span class="sxs-lookup"><span data-stu-id="73b7a-263">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="73b7a-264">Удаление обработчика или отключение наследования выполняется вместе с [настройкой базового пути приложения](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="73b7a-264">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="73b7a-265">Задайте базовый путь приложения в файле `index.html` приложения как псевдоним IIS, используемый при настройке дочернего приложения в IIS.</span><span class="sxs-lookup"><span data-stu-id="73b7a-265">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="73b7a-266">Алгоритмы сжатия Brotli и Gzip</span><span class="sxs-lookup"><span data-stu-id="73b7a-266">Brotli and Gzip compression</span></span>

<span data-ttu-id="73b7a-267">С помощью файла `web.config` можно настроить IIS для обслуживания ресурсов Blazor, сжатых с помощью алгоритма Brotli или Gzip.</span><span class="sxs-lookup"><span data-stu-id="73b7a-267">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="73b7a-268">Пример конфигурации см. по адресу [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="73b7a-268">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="73b7a-269">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="73b7a-269">Troubleshooting</span></span>

<span data-ttu-id="73b7a-270">Если получено сообщение *500 — Internal Server Error* (внутренняя ошибка сервера), а диспетчер служб IIS возвращает ошибки при попытке получить доступ к конфигурации веб сайта, убедитесь, что установлен модуль переопределения URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="73b7a-270">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="73b7a-271">Если модуль не установлен, IIS не может проанализировать файл `web.config`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-271">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="73b7a-272">Это предотвращает загрузку конфигурации веб сайта диспетчером служб IIS и передачу статических файлов Blazor c веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="73b7a-272">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="73b7a-273">Дополнительные сведения об устранении неполадок развертывания в службах IIS см. в разделе <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="73b7a-273">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="73b7a-274">Хранилище Azure</span><span class="sxs-lookup"><span data-stu-id="73b7a-274">Azure Storage</span></span>

<span data-ttu-id="73b7a-275">Размещение статических файлов [службы хранилища Azure](/azure/storage/) предусматривает размещение бессерверного приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="73b7a-275">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="73b7a-276">Поддерживаются пользовательские доменные имена, сеть доставки содержимого Azure (CDN) и HTTPS.</span><span class="sxs-lookup"><span data-stu-id="73b7a-276">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="73b7a-277">Если служба BLOB-объектов настроена для размещения статических веб-сайтов в учетной записи хранения:</span><span class="sxs-lookup"><span data-stu-id="73b7a-277">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="73b7a-278">Задайте для параметра **Имя документа индекса** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-278">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="73b7a-279">Задайте для параметра **Путь к документу с ошибкой** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-279">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="73b7a-280">Компоненты Razor и другие конечные точки, не являющиеся файлами, не имеют физических путей в статическом содержимом, хранящемся в службе BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="73b7a-280">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="73b7a-281">При получении запроса для одного из этих ресурсов, который должен обрабатываться маршрутизатором Blazor, ошибка *404 - Объект не найден*, создаваемая службой BLOB-объектов, перенаправляет запрос, используя **путь к документу с ошибкой**.</span><span class="sxs-lookup"><span data-stu-id="73b7a-281">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="73b7a-282">При этом возвращается большой двоичный объект `index.html`, и маршрутизатор Blazor загружает и обрабатывает путь.</span><span class="sxs-lookup"><span data-stu-id="73b7a-282">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="73b7a-283">Если файлы не загружаются во время выполнения из-за недопустимых типов MIME в заголовках файлов `Content-Type`, выполните одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="73b7a-283">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="73b7a-284">Настройте средства для установки правильных типов MIME (заголовков `Content-Type`) при развертывании файлов.</span><span class="sxs-lookup"><span data-stu-id="73b7a-284">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="73b7a-285">Измените типы MIME (заголовки `Content-Type`) для файлов после развертывания приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-285">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="73b7a-286">В Обозревателе службы хранилища (портал Azure) для каждого файла сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="73b7a-286">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="73b7a-287">Щелкните правой кнопкой мыши файл и выберите **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="73b7a-287">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="73b7a-288">Укажите значение для параметра **ContentType** и нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="73b7a-288">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="73b7a-289">См. подробнее о [размещении статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="73b7a-289">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="73b7a-290">Nginx</span><span class="sxs-lookup"><span data-stu-id="73b7a-290">Nginx</span></span>

<span data-ttu-id="73b7a-291">Следующий файл `nginx.conf` упрощен для демонстрации того, как настроить Nginx для отправки файла `index.html`, когда не удается найти соответствующий файл на диске.</span><span class="sxs-lookup"><span data-stu-id="73b7a-291">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="73b7a-292">При задании [ограничения пиковой скорости NGINX](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) с помощью [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) приложениям Blazor WebAssembly может потребоваться большое значение параметра `burst` с учетом сравнительно большого количества запросов, выполняемых приложением.</span><span class="sxs-lookup"><span data-stu-id="73b7a-292">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="73b7a-293">Изначально задайте для этого параметра значение не менее 60.</span><span class="sxs-lookup"><span data-stu-id="73b7a-293">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="73b7a-294">Увеличьте значение, если средства разработчика в браузере или средство контроля сетевого трафика сообщают о том, что запросы возвращают код состояния *503, служба недоступна*.</span><span class="sxs-lookup"><span data-stu-id="73b7a-294">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="73b7a-295">Дополнительные сведения о конфигурации веб-сервера Nginx в рабочей среде см. в разделе [Создание файлов конфигурации NGINX Plus и NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="73b7a-295">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="73b7a-296">Nginx в Docker</span><span class="sxs-lookup"><span data-stu-id="73b7a-296">Nginx in Docker</span></span>

<span data-ttu-id="73b7a-297">Чтобы разместить Blazor в Docker с помощью Nginx, настройте в Dockerfile образ с Nginx на основе Alpine.</span><span class="sxs-lookup"><span data-stu-id="73b7a-297">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="73b7a-298">Измените Dockerfile, скопировав файл `nginx.config` в контейнер.</span><span class="sxs-lookup"><span data-stu-id="73b7a-298">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="73b7a-299">Добавьте одну строку в Dockerfile, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="73b7a-299">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="73b7a-300">Apache</span><span class="sxs-lookup"><span data-stu-id="73b7a-300">Apache</span></span>

<span data-ttu-id="73b7a-301">Чтобы развернуть приложение Blazor WebAssembly в CentOS 7 или более поздней версии, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="73b7a-301">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="73b7a-302">Создайте файл конфигурации Apache.</span><span class="sxs-lookup"><span data-stu-id="73b7a-302">Create the Apache configuration file.</span></span> <span data-ttu-id="73b7a-303">В следующем примере показан упрощенный файл конфигурации (`blazorapp.config`):</span><span class="sxs-lookup"><span data-stu-id="73b7a-303">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="73b7a-304">Поместите файл конфигурации Apache в каталог `/etc/httpd/conf.d/`. Это каталог конфигурации Apache по умолчанию в CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="73b7a-304">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="73b7a-305">Поместите файлы приложения в каталог `/var/www/blazorapp` (расположение, указанное в параметре `DocumentRoot` в файле конфигурации).</span><span class="sxs-lookup"><span data-stu-id="73b7a-305">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="73b7a-306">Перезапустите службу Apache.</span><span class="sxs-lookup"><span data-stu-id="73b7a-306">Restart the Apache service.</span></span>

<span data-ttu-id="73b7a-307">Дополнительные сведения см. в разделах [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) и [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="73b7a-307">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="73b7a-308">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="73b7a-308">GitHub Pages</span></span>

<span data-ttu-id="73b7a-309">Чтобы обеспечить переопределение URL-адресов, добавьте файл `wwwroot/404.html` со скриптом, который производит перенаправление запроса на страницу `index.html`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-309">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="73b7a-310">Пример см. в репозитории GitHub [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="73b7a-310">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="73b7a-311">[Пример веб-сайта](https://stevesandersonms.github.io/BlazorOnGitHubPages/)</span><span class="sxs-lookup"><span data-stu-id="73b7a-311">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="73b7a-312">При использовании сайта проекта вместо сайта организации следует обновить тег `<base>` в файле `wwwroot/index.html`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-312">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="73b7a-313">Задайте для имени репозитория GitHub значение атрибута `href` с косой чертой в конце (например, `/my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="73b7a-313">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="73b7a-314">В репозитории GitHub [SteveSandersonMS/BlazorOnGitHubPages ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)база `href` обновляется при публикации [файлом конфигурации `.github/workflows/main.yml`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="73b7a-314">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="73b7a-315">[Репозиторий GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) не принадлежит, не обслуживается или не поддерживается .NET Foundation или корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="73b7a-315">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="73b7a-316">Значения конфигурации узла</span><span class="sxs-lookup"><span data-stu-id="73b7a-316">Host configuration values</span></span>

<span data-ttu-id="73b7a-317">[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) могут принимать следующие значения конфигурации узла в качестве аргументов командной строки во время выполнения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="73b7a-317">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="73b7a-318">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="73b7a-318">Content root</span></span>

<span data-ttu-id="73b7a-319">Аргумент `--contentroot` задает абсолютный путь к каталогу, где находятся файлы содержимого приложения ([корневой каталог содержимого](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="73b7a-319">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="73b7a-320">В следующих примерах `/content-root-path` — это путь к корневому каталогу содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-320">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="73b7a-321">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="73b7a-321">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="73b7a-322">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="73b7a-322">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="73b7a-323">Добавьте запись в файл приложения `launchSettings.json` в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="73b7a-323">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="73b7a-324">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-324">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="73b7a-325">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="73b7a-325">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="73b7a-326">Задание аргумента на странице свойств Visual Studio добавляет его в файл `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-326">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="73b7a-327">Базовый путь</span><span class="sxs-lookup"><span data-stu-id="73b7a-327">Path base</span></span>

<span data-ttu-id="73b7a-328">Аргумент `--pathbase` задает базовый путь приложения для локального выполнения с некорневым относительным путем URL (в `<base>` тег `href` задан с путем, отличным от `/` для промежуточной и рабочей сред).</span><span class="sxs-lookup"><span data-stu-id="73b7a-328">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="73b7a-329">В следующих примерах `/relative-URL-path` — это базовый путь приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-329">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="73b7a-330">Дополнительные сведения см. в описании [базового пути приложения](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="73b7a-330">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="73b7a-331">В отличие от пути, заданного через `href` в теге `<base>`, не ставьте косую черту (`/`) при передаче значения аргумента `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-331">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="73b7a-332">Если основной путь приложения задан в теге `<base>` как `<base href="/CoolApp/">` (включая косую черту в конце), передайте значение аргумента командной строки как `--pathbase=/CoolApp` (без косой черты в конце).</span><span class="sxs-lookup"><span data-stu-id="73b7a-332">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="73b7a-333">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="73b7a-333">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="73b7a-334">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="73b7a-334">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="73b7a-335">Добавьте запись в файл приложения `launchSettings.json` в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="73b7a-335">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="73b7a-336">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-336">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="73b7a-337">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="73b7a-337">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="73b7a-338">Задание аргумента на странице свойств Visual Studio добавляет его в файл `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-338">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="73b7a-339">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="73b7a-339">URLs</span></span>

<span data-ttu-id="73b7a-340">Аргумент `--urls` задает IP-адреса или адреса узлов с портами и протоколами, по которым ожидаются запросы.</span><span class="sxs-lookup"><span data-stu-id="73b7a-340">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="73b7a-341">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="73b7a-341">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="73b7a-342">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="73b7a-342">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="73b7a-343">Добавьте запись в файл приложения `launchSettings.json` в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="73b7a-343">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="73b7a-344">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-344">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="73b7a-345">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="73b7a-345">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="73b7a-346">Задание аргумента на странице свойств Visual Studio добавляет его в файл `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-346">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="73b7a-347">Настройка средства обрезки</span><span class="sxs-lookup"><span data-stu-id="73b7a-347">Configure the Trimmer</span></span>

<span data-ttu-id="73b7a-348">Blazor выполняет фильтрацию для промежуточного языка (IL) в каждой сборке выпуска, чтобы удалить ненужный промежуточный язык из выходных сборок.</span><span class="sxs-lookup"><span data-stu-id="73b7a-348">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="73b7a-349">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="73b7a-349">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="73b7a-350">Настройка компоновщика</span><span class="sxs-lookup"><span data-stu-id="73b7a-350">Configure the Linker</span></span>

<span data-ttu-id="73b7a-351">Blazor выполняет компоновку для промежуточного языка (IL) в каждой сборке выпуска, чтобы удалить ненужный промежуточный язык из выходных сборок.</span><span class="sxs-lookup"><span data-stu-id="73b7a-351">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="73b7a-352">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="73b7a-352">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="73b7a-353">Загрузка пользовательских ресурсов загрузки</span><span class="sxs-lookup"><span data-stu-id="73b7a-353">Custom boot resource loading</span></span>

<span data-ttu-id="73b7a-354">Приложение Blazor WebAssembly можно инициализировать с помощью функции `loadBootResource`, чтобы переопределить встроенный механизм загрузки ресурсов загрузки.</span><span class="sxs-lookup"><span data-stu-id="73b7a-354">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="73b7a-355">Используйте `loadBootResource` в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="73b7a-355">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="73b7a-356">Разрешить пользователям загружать статические ресурсы, такие как данные о часовом поясе или `dotnet.wasm`, из сети CDN.</span><span class="sxs-lookup"><span data-stu-id="73b7a-356">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="73b7a-357">Загрузить сжатые сборки с помощью HTTP-запроса и распаковать их на клиенте для узлов, которые не поддерживают получение сжатого содержимого с сервера.</span><span class="sxs-lookup"><span data-stu-id="73b7a-357">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="73b7a-358">Создать псевдоним ресурсов, выбрав другое имя путем перенаправления каждого запроса `fetch` на новое имя.</span><span class="sxs-lookup"><span data-stu-id="73b7a-358">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="73b7a-359">Параметры `loadBootResource` приведены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="73b7a-359">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="73b7a-360">Параметр</span><span class="sxs-lookup"><span data-stu-id="73b7a-360">Parameter</span></span>    | <span data-ttu-id="73b7a-361">Описание</span><span class="sxs-lookup"><span data-stu-id="73b7a-361">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="73b7a-362">Тип ресурса.</span><span class="sxs-lookup"><span data-stu-id="73b7a-362">The type of the resource.</span></span> <span data-ttu-id="73b7a-363">Допустимые типы: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="73b7a-363">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="73b7a-364">Имя ресурса.</span><span class="sxs-lookup"><span data-stu-id="73b7a-364">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="73b7a-365">Относительный или абсолютный URI ресурса.</span><span class="sxs-lookup"><span data-stu-id="73b7a-365">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="73b7a-366">Строка целостности, представляющая ожидаемое содержимое в ответе.</span><span class="sxs-lookup"><span data-stu-id="73b7a-366">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="73b7a-367">`loadBootResource` возвращает любой из следующих элементов для переопределения процесса загрузки.</span><span class="sxs-lookup"><span data-stu-id="73b7a-367">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="73b7a-368">Строка URI.</span><span class="sxs-lookup"><span data-stu-id="73b7a-368">URI string.</span></span> <span data-ttu-id="73b7a-369">В следующем примере (`wwwroot/index.html`) следующие файлы обслуживаются из сети CDN в `https://my-awesome-cdn.com/`:</span><span class="sxs-lookup"><span data-stu-id="73b7a-369">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="73b7a-370">Данные часового пояса</span><span class="sxs-lookup"><span data-stu-id="73b7a-370">Timezone data</span></span>

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

* <span data-ttu-id="73b7a-371">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-371">`Promise<Response>`.</span></span> <span data-ttu-id="73b7a-372">Передайте параметр `integrity` в заголовке для сохранения поведения проверки целостности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="73b7a-372">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="73b7a-373">В следующем примере (`wwwroot/index.html`) в исходящие запросы добавляется пользовательский заголовок HTTP и передается параметр `integrity` в вызов `fetch`:</span><span class="sxs-lookup"><span data-stu-id="73b7a-373">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="73b7a-374">`null`/`undefined`, что приводит к поведению загрузки по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="73b7a-374">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="73b7a-375">Внешние источники должны возвращать необходимые заголовки CORS для браузеров, чтобы разрешить загрузку ресурсов между источниками.</span><span class="sxs-lookup"><span data-stu-id="73b7a-375">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="73b7a-376">По умолчанию CDN обычно предоставляют необходимые заголовки.</span><span class="sxs-lookup"><span data-stu-id="73b7a-376">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="73b7a-377">Необходимо указать только типы для пользовательских поведений.</span><span class="sxs-lookup"><span data-stu-id="73b7a-377">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="73b7a-378">Типы, не указанные в `loadBootResource`, загружаются платформой в соответствии с поведением при загрузке по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="73b7a-378">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="73b7a-379">Изменение расширения DLL-файлов.</span><span class="sxs-lookup"><span data-stu-id="73b7a-379">Change the filename extension of DLL files</span></span>

<span data-ttu-id="73b7a-380">Если вам необходимо изменить расширения опубликованных `.dll`-файлов приложения, следуйте указаниям в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="73b7a-380">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="73b7a-381">После публикации приложения используйте скрипт оболочки или конвейер сборки DevOps для переименования `.dll`-файлов для использования другого расширения файла.</span><span class="sxs-lookup"><span data-stu-id="73b7a-381">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="73b7a-382">Указывайте на `.dll`-файлы в каталоге `wwwroot` опубликованных выходных данных приложения (например, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span><span class="sxs-lookup"><span data-stu-id="73b7a-382">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="73b7a-383">В следующих примерах `.dll`-файлы переименованы для использования расширения имени файла `.bin`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-383">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="73b7a-384">Windows:</span><span class="sxs-lookup"><span data-stu-id="73b7a-384">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="73b7a-385">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="73b7a-385">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="73b7a-386">В Linux или macOS.</span><span class="sxs-lookup"><span data-stu-id="73b7a-386">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="73b7a-387">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="73b7a-387">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="73b7a-388">Чтобы использовать расширение имени файла, отличное от `.bin`, замените `.bin` в предыдущих командах.</span><span class="sxs-lookup"><span data-stu-id="73b7a-388">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="73b7a-389">Для сжатых файлов `blazor.boot.json.gz` и `blazor.boot.json.br` используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="73b7a-389">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="73b7a-390">Удалите сжатые файлы `blazor.boot.json.gz` и `blazor.boot.json.br`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-390">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="73b7a-391">Сжатие при таком подходе отключается.</span><span class="sxs-lookup"><span data-stu-id="73b7a-391">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="73b7a-392">Выполните повторное сжатие обновленного файла `blazor.boot.json`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-392">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="73b7a-393">Приведенные выше рекомендации также применимы при использовании ресурсов рабочей роли.</span><span class="sxs-lookup"><span data-stu-id="73b7a-393">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="73b7a-394">Выполните удаление или повторное сжатие файлов `wwwroot/service-worker-assets.js.br` и `wwwroot/service-worker-assets.js.gz`.</span><span class="sxs-lookup"><span data-stu-id="73b7a-394">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="73b7a-395">иначе проверка целостности файлов в браузере завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="73b7a-395">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="73b7a-396">В следующем примере Windows используется скрипт PowerShell, размещенный в корне проекта.</span><span class="sxs-lookup"><span data-stu-id="73b7a-396">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="73b7a-397">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="73b7a-397">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="73b7a-398">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="73b7a-398">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="73b7a-399">В файле проекта скрипт запускается после публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="73b7a-399">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="73b7a-400">В случае переименования и отложенной загрузки тех же сборок см. инструкции в разделе <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span><span class="sxs-lookup"><span data-stu-id="73b7a-400">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>
