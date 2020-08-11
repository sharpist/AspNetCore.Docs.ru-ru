---
title: Размещение и развертывание ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и страниц GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 9d596e38a1d8350cd4a27f2fec4b262a0edf1015
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818850"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="62cf4-103">Размещение и развертывание ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="62cf4-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="62cf4-104">Авторы: [Люк Лэтем](https://github.com/guardrex) (Luke Latham), [Рэйнер Стропек](https://www.timecockpit.com) (Rainer Stropek), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth), [Бен Адамс](https://twitter.com/ben_a_adams) (Ben Adams) и [Сафиа Абдалла](https://safia.rocks) (Safia Abdalla).</span><span class="sxs-lookup"><span data-stu-id="62cf4-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="62cf4-105">При использовании [модели размещения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="62cf4-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="62cf4-106">Приложение Blazor, его зависимости и среда выполнения .NET скачиваются в браузере параллельно.</span><span class="sxs-lookup"><span data-stu-id="62cf4-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="62cf4-107">Приложение выполняется непосредственно в потоке пользовательского интерфейса браузера.</span><span class="sxs-lookup"><span data-stu-id="62cf4-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="62cf4-108">Поддерживаются следующие стратегии развертывания:</span><span class="sxs-lookup"><span data-stu-id="62cf4-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="62cf4-109">Приложение Blazor обслуживается приложением ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="62cf4-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="62cf4-110">Эта стратегия описана в разделе [Размещенное развертывание с использованием ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="62cf4-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="62cf4-111">Приложение Blazor помещается на статическом веб-сервере или службе, где .NET не используется, чтобы обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="62cf4-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="62cf4-112">Эта стратегия рассматривается в разделе [Автономное развертывание](#standalone-deployment), где приводятся сведения о размещении приложения Blazor WebAssembly в качестве дочернего приложения IIS.</span><span class="sxs-lookup"><span data-stu-id="62cf4-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="62cf4-113">Сжатие</span><span class="sxs-lookup"><span data-stu-id="62cf4-113">Compression</span></span>

<span data-ttu-id="62cf4-114">При публикации приложения Blazor WebAssembly выходные данные статически сжимаются, чтобы уменьшить размер приложения и исключить издержки на сжатие среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="62cf4-115">Используются следующие алгоритмы сжатия:</span><span class="sxs-lookup"><span data-stu-id="62cf4-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="62cf4-116">[Brotli](https://tools.ietf.org/html/rfc7932) (высший уровень)</span><span class="sxs-lookup"><span data-stu-id="62cf4-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="62cf4-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="62cf4-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="62cf4-118">Для обслуживания соответствующих сжатых файлов в Blazor используется ведущий проект.</span><span class="sxs-lookup"><span data-stu-id="62cf4-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="62cf4-119">При использовании размещенного проекта ASP.NET Core ведущий проект может выполнять согласование содержимого и обслуживать статически сжатые файлы.</span><span class="sxs-lookup"><span data-stu-id="62cf4-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="62cf4-120">При размещении автономного приложения Blazor WebAssembly, возможно, потребуется реализовать дополнительные действия для обслуживания статически сжатых файлов.</span><span class="sxs-lookup"><span data-stu-id="62cf4-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="62cf4-121">Сведения о конфигурации сжатия `web.config` IIS см. в статье [IIS: алгоритмы сжатия Brotli и Gzip](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="62cf4-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="62cf4-122">При размещении в статических решениях размещения, которые не поддерживают согласование содержимого статически сжатых файлов, например на страницах GitHub, рассмотрите возможность настройки приложения для извлечения и декодирования сжатых файлов Brotli:</span><span class="sxs-lookup"><span data-stu-id="62cf4-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="62cf4-123">Получите декодер JavaScript Brotli из [репозитория GitHub google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="62cf4-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="62cf4-124">По состоянию на июль 2020 г. файл декодера называется `decode.min.js` и находится в папке [`js` репозитория](https://github.com/google/brotli/tree/master/js).</span><span class="sxs-lookup"><span data-stu-id="62cf4-124">As of July 2020, the decoder file is named `decode.min.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  * <span data-ttu-id="62cf4-125">обновите приложение для использования декодера.</span><span class="sxs-lookup"><span data-stu-id="62cf4-125">Update the app to use the decoder.</span></span> <span data-ttu-id="62cf4-126">Измените разметку в закрывающем теге `<body>` в `wwwroot/index.html` следующим образом:</span><span class="sxs-lookup"><span data-stu-id="62cf4-126">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.min.js"></script>
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
 
<span data-ttu-id="62cf4-127">Чтобы отключить сжатие, добавьте свойство `BlazorEnableCompression` MSBuild в файл проекта приложения и задайте для него значение `false`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-127">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="62cf4-128">Переопределение URL-адресов для маршрутизации</span><span class="sxs-lookup"><span data-stu-id="62cf4-128">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="62cf4-129">Маршрутизация запросов к компонентам страниц в приложении Blazor WebAssembly не настолько проста, как маршрутизация запросов к размещенному приложению Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="62cf4-129">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="62cf4-130">Рассмотрим сценарий с приложением Blazor WebAssembly с двумя компонентами:</span><span class="sxs-lookup"><span data-stu-id="62cf4-130">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="62cf4-131">`Main.razor`. загружается в корне приложения и содержит ссылку на компонент `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="62cf4-131">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="62cf4-132">`About.razor`: компонент `About`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-132">`About.razor`: `About` component.</span></span>

<span data-ttu-id="62cf4-133">При запросе документа приложения по умолчанию в адресной строке браузера (например, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="62cf4-133">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="62cf4-134">Браузер отправляет запрос.</span><span class="sxs-lookup"><span data-stu-id="62cf4-134">The browser makes a request.</span></span>
1. <span data-ttu-id="62cf4-135">Возвращается страница по умолчанию; обычно это `index.html`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-135">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="62cf4-136">Страница `index.html` обеспечивает начальную загрузку приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-136">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="62cf4-137">Загружается маршрутизатор Blazor, и компонент Razor `Main` преобразовывается для просмотра.</span><span class="sxs-lookup"><span data-stu-id="62cf4-137">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="62cf4-138">На странице Main возможность выбора ссылки на компонент `About` доступна на клиентском компьютере, так как маршрутизатор Blazor не разрешает браузеру сделать запрос в Интернете к `www.contoso.com` для `About` и сам обрабатывает отображенный компонент `About`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-138">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="62cf4-139">Все запросы внутренних конечных точек *в пределах приложения Blazor WebAssembly* будут работать так же: Запросы не активируют браузерные запросы к ресурсам, размещенным на сервере в Интернете.</span><span class="sxs-lookup"><span data-stu-id="62cf4-139">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="62cf4-140">Маршрутизатор обрабатывает запросы внутренним образом.</span><span class="sxs-lookup"><span data-stu-id="62cf4-140">The router handles the requests internally.</span></span>

<span data-ttu-id="62cf4-141">Если запрос `www.contoso.com/About` сделан с помощью адресной строки браузера, он завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="62cf4-141">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="62cf4-142">Такой ресурс не существует на интернет-узле приложения, поэтому возвращается ответ *404 — Not Found* (не найдено).</span><span class="sxs-lookup"><span data-stu-id="62cf4-142">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="62cf4-143">Так как браузеры выполняют запросы к интернет-узлам для страниц на стороне клиента, веб-серверам и службам размещения необходимо переопределять все запросы к ресурсам, физически находящимся не на сервере, в случае страницы `index.html`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-143">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="62cf4-144">Когда `index.html` возвращается, маршрутизатор Blazor приложения берет на себя управление и отвечает необходимым ресурсом.</span><span class="sxs-lookup"><span data-stu-id="62cf4-144">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="62cf4-145">При развертывании на сервере IIS можно использовать модуль переопределения URL-адресов с опубликованным файлом `web.config` приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-145">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="62cf4-146">Дополнительные сведения см. в описании [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="62cf4-146">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="62cf4-147">Размещенное развертывание с использованием ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="62cf4-147">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="62cf4-148">*Размещенное развертывание* обслуживает приложение Blazor WebAssembly для браузеров из [приложения ASP.NET Core](xref:index), выполняющегося на веб-сервере.</span><span class="sxs-lookup"><span data-stu-id="62cf4-148">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="62cf4-149">Клиентское приложение Blazor WebAssembly будет опубликовано в папку `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` серверного приложения вместе со статическими веб-ресурсами серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-149">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="62cf4-150">Оба приложения развертываются вместе.</span><span class="sxs-lookup"><span data-stu-id="62cf4-150">The two apps are deployed together.</span></span> <span data-ttu-id="62cf4-151">Веб-сервер, позволяющий размещать приложения ASP.NET Core, является обязательным.</span><span class="sxs-lookup"><span data-stu-id="62cf4-151">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="62cf4-152">Для размещаемого развертывания Visual Studio включает шаблон проекта **Приложение Blazor WebAssembly** (шаблон `blazorwasm` при использовании команды [`dotnet new`](/dotnet/core/tools/dotnet-new)) с выбранным параметром **`Hosted`** (`-ho|--hosted` при использовании команды `dotnet new`).</span><span class="sxs-lookup"><span data-stu-id="62cf4-152">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="62cf4-153">Дополнительные сведения о размещении приложения ASP.NET Core и его развертывании см. в разделе <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="62cf4-153">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="62cf4-154">Подробнее о развертывании в службе приложений Azure см. в <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="62cf4-154">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="62cf4-155">Размещенное развертывание с несколькими приложениями Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="62cf4-155">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="62cf4-156">Конфигурация приложения</span><span class="sxs-lookup"><span data-stu-id="62cf4-156">App configuration</span></span>

<span data-ttu-id="62cf4-157">Чтобы настроить размещенное решение Blazor для обслуживания нескольких приложений Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="62cf4-157">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="62cf4-158">Используйте существующее размещенное решение Blazor или создайте новое решение из размещенного шаблона проекта Blazor.</span><span class="sxs-lookup"><span data-stu-id="62cf4-158">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="62cf4-159">В файле проекта клиентского приложения добавьте свойство `<StaticWebAssetBasePath>` в `<PropertyGroup>` со значением `FirstApp`, чтобы задать базовый путь для статических ресурсов проекта.</span><span class="sxs-lookup"><span data-stu-id="62cf4-159">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="62cf4-160">Добавьте в решение второе клиентское приложение:</span><span class="sxs-lookup"><span data-stu-id="62cf4-160">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="62cf4-161">Добавьте папку с именем `SecondClient` в папку решения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-161">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="62cf4-162">Создайте приложение Blazor WebAssembly с именем `SecondBlazorApp.Client` в папке `SecondClient` из шаблона проекта Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="62cf4-162">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="62cf4-163">В файле проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="62cf4-163">In the app's project file:</span></span>

    * <span data-ttu-id="62cf4-164">Добавьте свойство `<StaticWebAssetBasePath>` в `<PropertyGroup>` со значением `SecondApp`:</span><span class="sxs-lookup"><span data-stu-id="62cf4-164">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="62cf4-165">Добавьте ссылку на проект в проект `Shared`:</span><span class="sxs-lookup"><span data-stu-id="62cf4-165">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="62cf4-166">Заполнитель `{SOLUTION NAME}` — это имя решения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-166">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="62cf4-167">В файле проекта серверного приложения создайте ссылку на проект для добавленного клиентского приложения:</span><span class="sxs-lookup"><span data-stu-id="62cf4-167">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="62cf4-168">В файле серверного приложения `Properties/launchSettings.json` настройте `applicationUrl` профиля Kestrel (`{SOLUTION NAME}.Server`), чтобы получить доступ к клиентским приложениям через порты 5001 и 5002:</span><span class="sxs-lookup"><span data-stu-id="62cf4-168">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="62cf4-169">В методе `Startup.Configure` серверного приложения (`Startup.cs`) удалите эти строки, которые появляются после вызова <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span><span class="sxs-lookup"><span data-stu-id="62cf4-169">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

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

  <span data-ttu-id="62cf4-170">Добавьте ПО промежуточного слоя, которое сопоставляет запросы с клиентскими приложениями.</span><span class="sxs-lookup"><span data-stu-id="62cf4-170">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="62cf4-171">В следующем примере выполняется настройка ПО промежуточного слоя для запуска в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="62cf4-171">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="62cf4-172">Порт запроса — 5001 для исходного клиентского приложения или 5002 для добавленного клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-172">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="62cf4-173">Узел запроса — `firstapp.com` для исходного клиентского приложения или `secondapp.com` для добавленного клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-173">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="62cf4-174">В примере, приведенном в этом разделе, требуется дополнительная настройка для:</span><span class="sxs-lookup"><span data-stu-id="62cf4-174">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="62cf4-175">Доступа к приложениям в примерах доменов узла `firstapp.com` и `secondapp.com`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-175">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="62cf4-176">Сертификатов для клиентских приложений, чтобы включить средства безопасности TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="62cf4-176">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="62cf4-177">Описание полной необходимой конфигурации выходит за рамки этой статьи и зависит от того, как размещается решение.</span><span class="sxs-lookup"><span data-stu-id="62cf4-177">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="62cf4-178">Дополнительные сведения см. в [статьях о размещении и развертывании](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="62cf4-178">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="62cf4-179">Поместите следующий код, где строки были удалены ранее:</span><span class="sxs-lookup"><span data-stu-id="62cf4-179">Place the following code where the lines were removed earlier:</span></span>

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

* <span data-ttu-id="62cf4-180">В контроллере прогноза погоды серверного приложения (`Controllers/WeatherForecastController.cs`) замените существующий маршрут (`[Route("[controller]")]`) к `WeatherForecastController` следующими маршрутами:</span><span class="sxs-lookup"><span data-stu-id="62cf4-180">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="62cf4-181">ПО промежуточного слоя, добавленное в метод `Startup.Configure` серверного приложения ранее, изменяет входящие запросы к `/WeatherForecast` на `/FirstApp/WeatherForecast` или `/SecondApp/WeatherForecast` в зависимости от порта (5001/5002) или домена (`firstapp.com`/`secondapp.com`).</span><span class="sxs-lookup"><span data-stu-id="62cf4-181">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="62cf4-182">Предыдущие маршруты контроллеров необходимы для возврата данных о погоде из серверного приложения в клиентские приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-182">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="62cf4-183">Статические ресурсы и библиотеки классов</span><span class="sxs-lookup"><span data-stu-id="62cf4-183">Static assets and class libraries</span></span>

<span data-ttu-id="62cf4-184">Для статических ресурсов используйте следующие подходы.</span><span class="sxs-lookup"><span data-stu-id="62cf4-184">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="62cf4-185">Если ресурс находится в папке `wwwroot` клиентского приложения, укажите пути к ним обычным образом:</span><span class="sxs-lookup"><span data-stu-id="62cf4-185">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="62cf4-186">Если ресурс находится в папке `wwwroot` [библиотеки классов (RCL) Razor](xref:blazor/components/class-libraries), ссылайтесь на статический ресурс в клиентском приложении, следуя инструкциям в статье об [RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="62cf4-186">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="62cf4-187">На компоненты, предоставляемые клиентскому приложению с помощью библиотеки классов, ссылаются обычным образом.</span><span class="sxs-lookup"><span data-stu-id="62cf4-187">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="62cf4-188">Если какие-либо компоненты нуждаются в таблицах стилей или файлах JavaScript, используйте один из следующих подходов для получения статических ресурсов:</span><span class="sxs-lookup"><span data-stu-id="62cf4-188">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="62cf4-189">Файл `wwwroot/index.html` клиентского приложения можно связать со статическими ресурсами (`<link>`).</span><span class="sxs-lookup"><span data-stu-id="62cf4-189">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="62cf4-190">Компонент может использовать [компонент `Link`](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) платформы для получения статических ресурсов.</span><span class="sxs-lookup"><span data-stu-id="62cf4-190">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="62cf4-191">Приведенные выше подходы демонстрируются в следующих примерах.</span><span class="sxs-lookup"><span data-stu-id="62cf4-191">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="62cf4-192">На компоненты, предоставляемые клиентскому приложению с помощью библиотеки классов, ссылаются обычным образом.</span><span class="sxs-lookup"><span data-stu-id="62cf4-192">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="62cf4-193">Если какие либо компоненты нуждаются в таблицах стилей или файлах JavaScript, то файл `wwwroot/index.html` клиентского приложения должен содержать правильные ссылки на статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="62cf4-193">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="62cf4-194">Эти подходы демонстрируются в следующих примерах.</span><span class="sxs-lookup"><span data-stu-id="62cf4-194">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="62cf4-195">Добавьте следующий компонент `Jeep` в одно из клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="62cf4-195">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="62cf4-196">Компонент `Jeep` использует:</span><span class="sxs-lookup"><span data-stu-id="62cf4-196">The `Jeep` component uses:</span></span>

* <span data-ttu-id="62cf4-197">изображение из папки `wwwroot` клиентского приложения (`jeep-cj.png`);</span><span class="sxs-lookup"><span data-stu-id="62cf4-197">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="62cf4-198">изображение из папки `wwwroot` (`jeep-yj.png`) [добавленной библиотеки компонентов Razor](xref:blazor/components/class-libraries) (`JeepImage`).</span><span class="sxs-lookup"><span data-stu-id="62cf4-198">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="62cf4-199">Пример компонента (`Component1`) создается автоматически шаблоном проекта RCL при добавлении библиотеки `JeepImage` в решение.</span><span class="sxs-lookup"><span data-stu-id="62cf4-199">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="62cf4-200">**Не** публикуйте изображения автомобилей, если вы не являетесь владельцем этих изображений.</span><span class="sxs-lookup"><span data-stu-id="62cf4-200">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="62cf4-201">В противном случае вы рискуете нарушить авторские права.</span><span class="sxs-lookup"><span data-stu-id="62cf4-201">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="62cf4-202">Изображение `jeep-yj.png` из библиотеки также можно добавить в компонент `Component1` (`Component1.razor`) библиотеки.</span><span class="sxs-lookup"><span data-stu-id="62cf4-202">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="62cf4-203">Чтобы предоставить класс CSS `my-component` для страницы клиентского приложения, свяжите его с таблицей стилей библиотеки, используя [компонент `Link`](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) платформы:</span><span class="sxs-lookup"><span data-stu-id="62cf4-203">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

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

<span data-ttu-id="62cf4-204">Альтернативой использованию [компонента `Link`](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) является загрузка таблицы стилей из файла `wwwroot/index.html` клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-204">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="62cf4-205">Такой подход делает таблицу стилей доступной для всех компонентов в клиентском приложении:</span><span class="sxs-lookup"><span data-stu-id="62cf4-205">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="62cf4-206">Изображение `jeep-yj.png` из библиотеки также можно добавить в компонент `Component1` (`Component1.razor`) библиотеки.</span><span class="sxs-lookup"><span data-stu-id="62cf4-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

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

<span data-ttu-id="62cf4-207">Файл `wwwroot/index.html` клиентского приложения запрашивает таблицу стилей библиотеки со следующим добавленным тегом `<link>`:</span><span class="sxs-lookup"><span data-stu-id="62cf4-207">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="62cf4-208">Добавьте навигацию в компонент `Jeep` в компоненте `NavMenu` клиентского приложения (`Shared/NavMenu.razor`):</span><span class="sxs-lookup"><span data-stu-id="62cf4-208">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="62cf4-209">Дополнительные сведения об RCL см. здесь:</span><span class="sxs-lookup"><span data-stu-id="62cf4-209">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="62cf4-210">Автономное развертывание</span><span class="sxs-lookup"><span data-stu-id="62cf4-210">Standalone deployment</span></span>

<span data-ttu-id="62cf4-211">*Автономное развертывание* обрабатывает приложение Blazor WebAssembly как набор статических файлов, которые будут запрашиваться непосредственно клиентами.</span><span class="sxs-lookup"><span data-stu-id="62cf4-211">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="62cf4-212">Любой статический файловый сервер способен обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="62cf4-212">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="62cf4-213">Ресурсы автономного развертывания публикуются в папке `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-213">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="62cf4-214">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="62cf4-214">Azure App Service</span></span>

<span data-ttu-id="62cf4-215">Приложения Blazor WebAssembly можно развернуть в службах приложений Azure в Windows. При этом приложение размещается в службах [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="62cf4-215">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="62cf4-216">Развертывание автономного приложения Blazor WebAssembly в Службе приложений Azure для Linux в настоящее время не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="62cf4-216">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="62cf4-217">Сейчас образ сервера Linux для размещения приложения недоступен.</span><span class="sxs-lookup"><span data-stu-id="62cf4-217">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="62cf4-218">Ведется работа над реализацией этого сценария.</span><span class="sxs-lookup"><span data-stu-id="62cf4-218">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="62cf4-219">IIS</span><span class="sxs-lookup"><span data-stu-id="62cf4-219">IIS</span></span>

<span data-ttu-id="62cf4-220">IIS можно использовать как полнофункциональный статический файловый сервер для приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="62cf4-220">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="62cf4-221">Чтобы настроить IIS для размещения Blazor, перейдите к разделу [Развертывание статического веб-сайта на IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="62cf4-221">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="62cf4-222">Опубликованные ресурсы создаются в папке `/bin/Release/{TARGET FRAMEWORK}/publish`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-222">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="62cf4-223">Разместите содержимое папки `publish` на веб-сервере или в службе размещения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-223">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="62cf4-224">web.config.</span><span class="sxs-lookup"><span data-stu-id="62cf4-224">web.config</span></span>

<span data-ttu-id="62cf4-225">При публикации проекта Blazor создается файл `web.config` со следующей конфигурацией IIS:</span><span class="sxs-lookup"><span data-stu-id="62cf4-225">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="62cf4-226">Типы MIME заданы для следующих расширений файлов:</span><span class="sxs-lookup"><span data-stu-id="62cf4-226">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="62cf4-227">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="62cf4-227">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="62cf4-228">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="62cf4-228">`.json`: `application/json`</span></span>
  * <span data-ttu-id="62cf4-229">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="62cf4-229">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="62cf4-230">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="62cf4-230">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="62cf4-231">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="62cf4-231">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="62cf4-232">Сжатие HTTP включено для следующих типов MIME:</span><span class="sxs-lookup"><span data-stu-id="62cf4-232">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="62cf4-233">Задаются правила модуля переопределения URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="62cf4-233">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="62cf4-234">Предоставление подкаталога для размещения статических ресурсов приложения (`wwwroot/{PATH REQUESTED}`).</span><span class="sxs-lookup"><span data-stu-id="62cf4-234">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="62cf4-235">Создание резервной маршрутизации для одностраничного приложения, которая позволяет перенаправлять запросы, относящиеся к нефайловым ресурсам, к документу приложения по умолчанию в папке статических ресурсов (`wwwroot/index.html`).</span><span class="sxs-lookup"><span data-stu-id="62cf4-235">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="62cf4-236">Использование пользовательского файла web.config</span><span class="sxs-lookup"><span data-stu-id="62cf4-236">Use a custom web.config</span></span>

<span data-ttu-id="62cf4-237">Чтобы применить пользовательский файл `web.config`, поместите пользовательский файл `web.config` в корневую папку проекта и опубликуйте проект.</span><span class="sxs-lookup"><span data-stu-id="62cf4-237">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="62cf4-238">Установка модуля переопределения URL-адресов</span><span class="sxs-lookup"><span data-stu-id="62cf4-238">Install the URL Rewrite Module</span></span>

<span data-ttu-id="62cf4-239">[Модуль переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite) нужен, чтобы переопределять URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="62cf4-239">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="62cf4-240">Модуль не устанавливается по умолчанию и недоступен для установки как компонент службы роли веб-сервера (IIS).</span><span class="sxs-lookup"><span data-stu-id="62cf4-240">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="62cf4-241">Модуль необходимо скачать с веб-сайта IIS.</span><span class="sxs-lookup"><span data-stu-id="62cf4-241">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="62cf4-242">Для его установки используйте установщик веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="62cf4-242">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="62cf4-243">Локально перейдите на [страницу скачивания модуля переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="62cf4-243">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="62cf4-244">В англоязычной версии выберите **WebPI** для скачивания установщика веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="62cf4-244">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="62cf4-245">Для других языков выберите подходящую архитектуру сервера (x86 или x64) для скачивания установщика.</span><span class="sxs-lookup"><span data-stu-id="62cf4-245">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="62cf4-246">Скопируйте установщик на сервер.</span><span class="sxs-lookup"><span data-stu-id="62cf4-246">Copy the installer to the server.</span></span> <span data-ttu-id="62cf4-247">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="62cf4-247">Run the installer.</span></span> <span data-ttu-id="62cf4-248">Нажмите кнопку **Установить** и примите условия лицензионного соглашения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-248">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="62cf4-249">Перезагрузка сервера после завершения установки не требуется.</span><span class="sxs-lookup"><span data-stu-id="62cf4-249">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="62cf4-250">Настройка веб-сайта</span><span class="sxs-lookup"><span data-stu-id="62cf4-250">Configure the website</span></span>

<span data-ttu-id="62cf4-251">Установите для веб сайта **физический путь** к папке приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-251">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="62cf4-252">Папка содержит:</span><span class="sxs-lookup"><span data-stu-id="62cf4-252">The folder contains:</span></span>

* <span data-ttu-id="62cf4-253">Файл `web.config`, который используется службами IIS для настройки веб-сайта, включая необходимые правила перенаправления и типы содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="62cf4-253">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="62cf4-254">Папку статических ресурсов приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-254">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="62cf4-255">Размещение в качестве дочернего приложения IIS</span><span class="sxs-lookup"><span data-stu-id="62cf4-255">Host as an IIS sub-app</span></span>

<span data-ttu-id="62cf4-256">Если автономное приложение размещено как дочернее приложение IIS, выполните одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="62cf4-256">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="62cf4-257">Отключите наследуемый обработчик модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="62cf4-257">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="62cf4-258">Удалите обработчик в опубликованном файле `web.config` приложения Blazor, добавив раздел `<handlers>` в файл:</span><span class="sxs-lookup"><span data-stu-id="62cf4-258">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="62cf4-259">Отключите наследование раздела `<system.webServer>` от корневого (родительского) приложения, используя `<location>` со значением `false` для `inheritInChildApplications`:</span><span class="sxs-lookup"><span data-stu-id="62cf4-259">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="62cf4-260">Удаление обработчика или отключение наследования выполняется вместе с [настройкой базового пути приложения](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="62cf4-260">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="62cf4-261">Задайте базовый путь приложения в файле `index.html` приложения как псевдоним IIS, используемый при настройке дочернего приложения в IIS.</span><span class="sxs-lookup"><span data-stu-id="62cf4-261">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="62cf4-262">Алгоритмы сжатия Brotli и Gzip</span><span class="sxs-lookup"><span data-stu-id="62cf4-262">Brotli and Gzip compression</span></span>

<span data-ttu-id="62cf4-263">С помощью файла `web.config` можно настроить IIS для обслуживания ресурсов Blazor, сжатых с помощью алгоритма Brotli или Gzip.</span><span class="sxs-lookup"><span data-stu-id="62cf4-263">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="62cf4-264">Пример конфигурации см. по адресу [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="62cf4-264">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="62cf4-265">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="62cf4-265">Troubleshooting</span></span>

<span data-ttu-id="62cf4-266">Если получено сообщение *500 — Internal Server Error* (внутренняя ошибка сервера), а диспетчер служб IIS возвращает ошибки при попытке получить доступ к конфигурации веб сайта, убедитесь, что установлен модуль переопределения URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="62cf4-266">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="62cf4-267">Если модуль не установлен, IIS не может проанализировать файл `web.config`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-267">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="62cf4-268">Это предотвращает загрузку конфигурации веб сайта диспетчером служб IIS и передачу статических файлов Blazor c веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="62cf4-268">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="62cf4-269">Дополнительные сведения об устранении неполадок развертывания в службах IIS см. в разделе <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="62cf4-269">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="62cf4-270">Хранилище Azure</span><span class="sxs-lookup"><span data-stu-id="62cf4-270">Azure Storage</span></span>

<span data-ttu-id="62cf4-271">Размещение статических файлов [службы хранилища Azure](/azure/storage/) предусматривает размещение бессерверного приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="62cf4-271">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="62cf4-272">Поддерживаются пользовательские доменные имена, сеть доставки содержимого Azure (CDN) и HTTPS.</span><span class="sxs-lookup"><span data-stu-id="62cf4-272">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="62cf4-273">Если служба BLOB-объектов настроена для размещения статических веб-сайтов в учетной записи хранения:</span><span class="sxs-lookup"><span data-stu-id="62cf4-273">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="62cf4-274">Задайте для параметра **Имя документа индекса** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-274">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="62cf4-275">Задайте для параметра **Путь к документу с ошибкой** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-275">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="62cf4-276">Компоненты Razor и другие конечные точки, не являющиеся файлами, не имеют физических путей в статическом содержимом, хранящемся в службе BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="62cf4-276">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="62cf4-277">При получении запроса для одного из этих ресурсов, который должен обрабатываться маршрутизатором Blazor, ошибка *404 - Объект не найден*, создаваемая службой BLOB-объектов, перенаправляет запрос, используя **путь к документу с ошибкой**.</span><span class="sxs-lookup"><span data-stu-id="62cf4-277">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="62cf4-278">При этом возвращается большой двоичный объект `index.html`, и маршрутизатор Blazor загружает и обрабатывает путь.</span><span class="sxs-lookup"><span data-stu-id="62cf4-278">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="62cf4-279">Если файлы не загружаются во время выполнения из-за недопустимых типов MIME в заголовках файлов `Content-Type`, выполните одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="62cf4-279">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="62cf4-280">Настройте средства для установки правильных типов MIME (заголовков `Content-Type`) при развертывании файлов.</span><span class="sxs-lookup"><span data-stu-id="62cf4-280">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="62cf4-281">Измените типы MIME (заголовки `Content-Type`) для файлов после развертывания приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-281">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="62cf4-282">В Обозревателе службы хранилища (портал Azure) для каждого файла сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="62cf4-282">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="62cf4-283">Щелкните правой кнопкой мыши файл и выберите **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="62cf4-283">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="62cf4-284">Укажите значение для параметра **ContentType** и нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="62cf4-284">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="62cf4-285">См. подробнее о [размещении статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="62cf4-285">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="62cf4-286">Nginx</span><span class="sxs-lookup"><span data-stu-id="62cf4-286">Nginx</span></span>

<span data-ttu-id="62cf4-287">Следующий файл `nginx.conf` упрощен для демонстрации того, как настроить Nginx для отправки файла `index.html`, когда не удается найти соответствующий файл на диске.</span><span class="sxs-lookup"><span data-stu-id="62cf4-287">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="62cf4-288">При задании [ограничения пиковой скорости NGINX](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) с помощью [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) приложениям Blazor WebAssembly может потребоваться большое значение параметра `burst` с учетом сравнительно большого количества запросов, выполняемых приложением.</span><span class="sxs-lookup"><span data-stu-id="62cf4-288">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="62cf4-289">Изначально задайте для этого параметра значение не менее 60.</span><span class="sxs-lookup"><span data-stu-id="62cf4-289">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="62cf4-290">Увеличьте значение, если средства разработчика в браузере или средство контроля сетевого трафика сообщают о том, что запросы возвращают код состояния *503, служба недоступна*.</span><span class="sxs-lookup"><span data-stu-id="62cf4-290">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="62cf4-291">Дополнительные сведения о конфигурации веб-сервера Nginx в рабочей среде см. в разделе [Создание файлов конфигурации NGINX Plus и NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="62cf4-291">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="62cf4-292">Nginx в Docker</span><span class="sxs-lookup"><span data-stu-id="62cf4-292">Nginx in Docker</span></span>

<span data-ttu-id="62cf4-293">Чтобы разместить Blazor в Docker с помощью Nginx, настройте в Dockerfile образ с Nginx на основе Alpine.</span><span class="sxs-lookup"><span data-stu-id="62cf4-293">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="62cf4-294">Измените Dockerfile, скопировав файл `nginx.config` в контейнер.</span><span class="sxs-lookup"><span data-stu-id="62cf4-294">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="62cf4-295">Добавьте одну строку в Dockerfile, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="62cf4-295">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="62cf4-296">Apache</span><span class="sxs-lookup"><span data-stu-id="62cf4-296">Apache</span></span>

<span data-ttu-id="62cf4-297">Чтобы развернуть приложение Blazor WebAssembly в CentOS 7 или более поздней версии, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="62cf4-297">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="62cf4-298">Создайте файл конфигурации Apache.</span><span class="sxs-lookup"><span data-stu-id="62cf4-298">Create the Apache configuration file.</span></span> <span data-ttu-id="62cf4-299">В следующем примере показан упрощенный файл конфигурации (`blazorapp.config`):</span><span class="sxs-lookup"><span data-stu-id="62cf4-299">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="62cf4-300">Поместите файл конфигурации Apache в каталог `/etc/httpd/conf.d/`. Это каталог конфигурации Apache по умолчанию в CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="62cf4-300">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="62cf4-301">Поместите файлы приложения в каталог `/var/www/blazorapp` (расположение, указанное в параметре `DocumentRoot` в файле конфигурации).</span><span class="sxs-lookup"><span data-stu-id="62cf4-301">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="62cf4-302">Перезапустите службу Apache.</span><span class="sxs-lookup"><span data-stu-id="62cf4-302">Restart the Apache service.</span></span>

<span data-ttu-id="62cf4-303">Дополнительные сведения см. в разделах [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) и [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="62cf4-303">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="62cf4-304">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="62cf4-304">GitHub Pages</span></span>

<span data-ttu-id="62cf4-305">Чтобы обеспечить переопределение URL-адресов, добавьте файл `wwwroot/404.html` со скриптом, который производит перенаправление запроса на страницу `index.html`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-305">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="62cf4-306">Пример см. в репозитории GitHub [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="62cf4-306">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="62cf4-307">[Пример веб-сайта](https://stevesandersonms.github.io/BlazorOnGitHubPages/)</span><span class="sxs-lookup"><span data-stu-id="62cf4-307">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="62cf4-308">При использовании сайта проекта вместо сайта организации следует обновить тег `<base>` в файле `wwwroot/index.html`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-308">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="62cf4-309">Задайте для имени репозитория GitHub значение атрибута `href` с косой чертой в конце (например, `/my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="62cf4-309">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="62cf4-310">В репозитории GitHub [SteveSandersonMS/BlazorOnGitHubPages ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)база `href` обновляется при публикации [файлом конфигурации `.github/workflows/main.yml`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="62cf4-310">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="62cf4-311">[Репозиторий GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) не принадлежит, не обслуживается или не поддерживается .NET Foundation или корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="62cf4-311">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="62cf4-312">Значения конфигурации узла</span><span class="sxs-lookup"><span data-stu-id="62cf4-312">Host configuration values</span></span>

<span data-ttu-id="62cf4-313">[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) могут принимать следующие значения конфигурации узла в качестве аргументов командной строки во время выполнения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="62cf4-313">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="62cf4-314">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="62cf4-314">Content root</span></span>

<span data-ttu-id="62cf4-315">Аргумент `--contentroot` задает абсолютный путь к каталогу, где находятся файлы содержимого приложения ([корневой каталог содержимого](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="62cf4-315">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="62cf4-316">В следующих примерах `/content-root-path` — это путь к корневому каталогу содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-316">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="62cf4-317">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="62cf4-317">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="62cf4-318">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="62cf4-318">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="62cf4-319">Добавьте запись в файл приложения `launchSettings.json` в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="62cf4-319">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="62cf4-320">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-320">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="62cf4-321">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="62cf4-321">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="62cf4-322">Задание аргумента на странице свойств Visual Studio добавляет его в файл `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-322">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="62cf4-323">Базовый путь</span><span class="sxs-lookup"><span data-stu-id="62cf4-323">Path base</span></span>

<span data-ttu-id="62cf4-324">Аргумент `--pathbase` задает базовый путь приложения для локального выполнения с некорневым относительным путем URL (в `<base>` тег `href` задан с путем, отличным от `/` для промежуточной и рабочей сред).</span><span class="sxs-lookup"><span data-stu-id="62cf4-324">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="62cf4-325">В следующих примерах `/relative-URL-path` — это базовый путь приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-325">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="62cf4-326">Дополнительные сведения см. в описании [базового пути приложения](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="62cf4-326">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="62cf4-327">В отличие от пути, заданного через `href` в теге `<base>`, не ставьте косую черту (`/`) при передаче значения аргумента `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-327">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="62cf4-328">Если основной путь приложения задан в теге `<base>` как `<base href="/CoolApp/">` (включая косую черту в конце), передайте значение аргумента командной строки как `--pathbase=/CoolApp` (без косой черты в конце).</span><span class="sxs-lookup"><span data-stu-id="62cf4-328">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="62cf4-329">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="62cf4-329">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="62cf4-330">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="62cf4-330">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="62cf4-331">Добавьте запись в файл приложения `launchSettings.json` в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="62cf4-331">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="62cf4-332">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-332">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="62cf4-333">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="62cf4-333">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="62cf4-334">Задание аргумента на странице свойств Visual Studio добавляет его в файл `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-334">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="62cf4-335">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="62cf4-335">URLs</span></span>

<span data-ttu-id="62cf4-336">Аргумент `--urls` задает IP-адреса или адреса узлов с портами и протоколами, по которым ожидаются запросы.</span><span class="sxs-lookup"><span data-stu-id="62cf4-336">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="62cf4-337">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="62cf4-337">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="62cf4-338">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="62cf4-338">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="62cf4-339">Добавьте запись в файл приложения `launchSettings.json` в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="62cf4-339">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="62cf4-340">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-340">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="62cf4-341">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="62cf4-341">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="62cf4-342">Задание аргумента на странице свойств Visual Studio добавляет его в файл `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-342">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="62cf4-343">Настройка компоновщика</span><span class="sxs-lookup"><span data-stu-id="62cf4-343">Configure the Linker</span></span>

<span data-ttu-id="62cf4-344">Blazor выполняет компоновку для промежуточного языка (IL) в каждой сборке выпуска, чтобы удалить ненужный промежуточный язык из выходных сборок.</span><span class="sxs-lookup"><span data-stu-id="62cf4-344">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="62cf4-345">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="62cf4-345">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="62cf4-346">Загрузка пользовательских ресурсов загрузки</span><span class="sxs-lookup"><span data-stu-id="62cf4-346">Custom boot resource loading</span></span>

<span data-ttu-id="62cf4-347">Приложение Blazor WebAssembly можно инициализировать с помощью функции `loadBootResource`, чтобы переопределить встроенный механизм загрузки ресурсов загрузки.</span><span class="sxs-lookup"><span data-stu-id="62cf4-347">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="62cf4-348">Используйте `loadBootResource` в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="62cf4-348">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="62cf4-349">Разрешить пользователям загружать статические ресурсы, такие как данные о часовом поясе или `dotnet.wasm`, из сети CDN.</span><span class="sxs-lookup"><span data-stu-id="62cf4-349">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="62cf4-350">Загрузить сжатые сборки с помощью HTTP-запроса и распаковать их на клиенте для узлов, которые не поддерживают получение сжатого содержимого с сервера.</span><span class="sxs-lookup"><span data-stu-id="62cf4-350">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="62cf4-351">Создать псевдоним ресурсов, выбрав другое имя путем перенаправления каждого запроса `fetch` на новое имя.</span><span class="sxs-lookup"><span data-stu-id="62cf4-351">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="62cf4-352">Параметры `loadBootResource` приведены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="62cf4-352">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="62cf4-353">Параметр</span><span class="sxs-lookup"><span data-stu-id="62cf4-353">Parameter</span></span>    | <span data-ttu-id="62cf4-354">Описание</span><span class="sxs-lookup"><span data-stu-id="62cf4-354">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="62cf4-355">Тип ресурса.</span><span class="sxs-lookup"><span data-stu-id="62cf4-355">The type of the resource.</span></span> <span data-ttu-id="62cf4-356">Допустимые типы: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="62cf4-356">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="62cf4-357">Имя ресурса.</span><span class="sxs-lookup"><span data-stu-id="62cf4-357">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="62cf4-358">Относительный или абсолютный URI ресурса.</span><span class="sxs-lookup"><span data-stu-id="62cf4-358">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="62cf4-359">Строка целостности, представляющая ожидаемое содержимое в ответе.</span><span class="sxs-lookup"><span data-stu-id="62cf4-359">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="62cf4-360">`loadBootResource` возвращает любой из следующих элементов для переопределения процесса загрузки.</span><span class="sxs-lookup"><span data-stu-id="62cf4-360">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="62cf4-361">Строка URI.</span><span class="sxs-lookup"><span data-stu-id="62cf4-361">URI string.</span></span> <span data-ttu-id="62cf4-362">В следующем примере (`wwwroot/index.html`) следующие файлы обслуживаются из сети CDN в `https://my-awesome-cdn.com/`:</span><span class="sxs-lookup"><span data-stu-id="62cf4-362">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="62cf4-363">Данные часового пояса</span><span class="sxs-lookup"><span data-stu-id="62cf4-363">Timezone data</span></span>

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

* <span data-ttu-id="62cf4-364">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-364">`Promise<Response>`.</span></span> <span data-ttu-id="62cf4-365">Передайте параметр `integrity` в заголовке для сохранения поведения проверки целостности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="62cf4-365">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="62cf4-366">В следующем примере (`wwwroot/index.html`) в исходящие запросы добавляется пользовательский заголовок HTTP и передается параметр `integrity` в вызов `fetch`:</span><span class="sxs-lookup"><span data-stu-id="62cf4-366">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="62cf4-367">`null`/`undefined`, что приводит к поведению загрузки по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="62cf4-367">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="62cf4-368">Внешние источники должны возвращать необходимые заголовки CORS для браузеров, чтобы разрешить загрузку ресурсов между источниками.</span><span class="sxs-lookup"><span data-stu-id="62cf4-368">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="62cf4-369">По умолчанию CDN обычно предоставляют необходимые заголовки.</span><span class="sxs-lookup"><span data-stu-id="62cf4-369">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="62cf4-370">Необходимо указать только типы для пользовательских поведений.</span><span class="sxs-lookup"><span data-stu-id="62cf4-370">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="62cf4-371">Типы, не указанные в `loadBootResource`, загружаются платформой в соответствии с поведением при загрузке по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="62cf4-371">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="62cf4-372">Изменение расширения DLL-файлов.</span><span class="sxs-lookup"><span data-stu-id="62cf4-372">Change the filename extension of DLL files</span></span>

<span data-ttu-id="62cf4-373">Если вам необходимо изменить расширения опубликованных `.dll`-файлов приложения, следуйте указаниям в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="62cf4-373">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="62cf4-374">После публикации приложения используйте скрипт оболочки или конвейер сборки DevOps для переименования `.dll`-файлов для использования другого расширения файла.</span><span class="sxs-lookup"><span data-stu-id="62cf4-374">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="62cf4-375">Указывайте на `.dll`-файлы в каталоге `wwwroot` опубликованных выходных данных приложения (например, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span><span class="sxs-lookup"><span data-stu-id="62cf4-375">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="62cf4-376">В следующих примерах `.dll`-файлы переименованы для использования расширения имени файла `.bin`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-376">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="62cf4-377">Windows:</span><span class="sxs-lookup"><span data-stu-id="62cf4-377">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="62cf4-378">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="62cf4-378">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="62cf4-379">В Linux или macOS.</span><span class="sxs-lookup"><span data-stu-id="62cf4-379">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="62cf4-380">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="62cf4-380">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="62cf4-381">Чтобы использовать расширение имени файла, отличное от `.bin`, замените `.bin` в предыдущих командах.</span><span class="sxs-lookup"><span data-stu-id="62cf4-381">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="62cf4-382">Для сжатых файлов `blazor.boot.json.gz` и `blazor.boot.json.br` используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="62cf4-382">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="62cf4-383">Удалите сжатые файлы `blazor.boot.json.gz` и `blazor.boot.json.br`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-383">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="62cf4-384">Сжатие при таком подходе отключается.</span><span class="sxs-lookup"><span data-stu-id="62cf4-384">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="62cf4-385">Выполните повторное сжатие обновленного файла `blazor.boot.json`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-385">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="62cf4-386">Приведенные выше рекомендации также применимы при использовании ресурсов рабочей роли.</span><span class="sxs-lookup"><span data-stu-id="62cf4-386">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="62cf4-387">Выполните удаление или повторное сжатие файлов `wwwroot/service-worker-assets.js.br` и `wwwroot/service-worker-assets.js.gz`.</span><span class="sxs-lookup"><span data-stu-id="62cf4-387">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="62cf4-388">иначе проверка целостности файлов в браузере завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="62cf4-388">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="62cf4-389">В следующем примере Windows используется скрипт PowerShell, размещенный в корне проекта.</span><span class="sxs-lookup"><span data-stu-id="62cf4-389">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="62cf4-390">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="62cf4-390">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="62cf4-391">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="62cf4-391">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="62cf4-392">В файле проекта скрипт запускается после публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="62cf4-392">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="62cf4-393">Чтобы оставить отзыв, перейдите на страницу [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="62cf4-393">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
