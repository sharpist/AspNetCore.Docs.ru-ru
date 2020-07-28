---
title: Среда размещения ASP.NET Core в операционной системе Linux с Nginx
author: rick-anderson
description: В статье описывается процедура настройки Nginx как обратного прокси-сервера на Ubuntu 16.04 для перенаправления трафика HTTP в веб-приложение ASP.NET Core, выполняемое в Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 8038c63200c7c9aaadb9e0e7a68ae315ff620197
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445298"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="d7921-103">Среда размещения ASP.NET Core в операционной системе Linux с Nginx</span><span class="sxs-lookup"><span data-stu-id="d7921-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="d7921-104">Автор [Сурабх Ширхатти](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="d7921-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="d7921-105">В этом руководстве описывается настройка готовой к работе среды ASP.NET Core на сервере Ubuntu 16.04.</span><span class="sxs-lookup"><span data-stu-id="d7921-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="d7921-106">Эти инструкции могут подходить для более поздних версий Ubuntu, но они еще не были протестированы в этих версиях.</span><span class="sxs-lookup"><span data-stu-id="d7921-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="d7921-107">Сведения о других дистрибутивах Linux, поддерживаемых платформой ASP.NET Core, см. в разделе о [необходимых компонентах для .NET Core в Linux](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="d7921-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="d7921-108">Для Ubuntu 14.04 в качестве решения для мониторинга процесса Kestrel рекомендуется *supervisord*.</span><span class="sxs-lookup"><span data-stu-id="d7921-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="d7921-109">Решение *systemd* в Ubuntu 14.04 недоступно.</span><span class="sxs-lookup"><span data-stu-id="d7921-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="d7921-110">Инструкции для Ubuntu 14.04 см. в [предыдущей версии этого раздела](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="d7921-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="d7921-111">В этом руководстве рассматривается</span><span class="sxs-lookup"><span data-stu-id="d7921-111">This guide:</span></span>

* <span data-ttu-id="d7921-112">размещение существующего приложения ASP.NET Core за обратным прокси-сервером;</span><span class="sxs-lookup"><span data-stu-id="d7921-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="d7921-113">настройка обратного прокси-сервера для перенаправления запросов на веб-сервер Kestrel;</span><span class="sxs-lookup"><span data-stu-id="d7921-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="d7921-114">проверка выполнения веб-приложения как управляющей программы при запуске системы;</span><span class="sxs-lookup"><span data-stu-id="d7921-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="d7921-115">настройка средства управления процессами, с помощью которого можно перезапустить веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="d7921-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d7921-116">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="d7921-116">Prerequisites</span></span>

1. <span data-ttu-id="d7921-117">Доступ к серверу Ubuntu 16.04 под учетной записью обычного пользователя с правами sudo.</span><span class="sxs-lookup"><span data-stu-id="d7921-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="d7921-118">Установите среду выполнения .NET Core на сервере.</span><span class="sxs-lookup"><span data-stu-id="d7921-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="d7921-119">Перейдите на [страницу скачивания .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="d7921-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="d7921-120">Выберите последнюю не предварительную версию .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7921-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="d7921-121">Скачайте последнюю не предварительную версию среды выполнения из таблицы под заголовком **Run apps — Runtime** (Выполнение приложений — среда выполнения).</span><span class="sxs-lookup"><span data-stu-id="d7921-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="d7921-122">Щелкните ссылку **Package manager instructions** (Инструкции диспетчера пакетов) для Linux и выполните инструкции для соответствующей версии Ubuntu.</span><span class="sxs-lookup"><span data-stu-id="d7921-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="d7921-123">Существующее приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7921-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="d7921-124">Перезапустить приложения ASP.NET Core, размещенные на сервере, можно в любой момент после обновления общей платформы.</span><span class="sxs-lookup"><span data-stu-id="d7921-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="d7921-125">Публикация и копирование приложения</span><span class="sxs-lookup"><span data-stu-id="d7921-125">Publish and copy over the app</span></span>

<span data-ttu-id="d7921-126">Настройте приложение, чтобы [его развертывание зависело от платформы](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="d7921-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="d7921-127">Если приложение запускается локально и не настроено для безопасного подключения (HTTPS), следует применять один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="d7921-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="d7921-128">Настройка приложения для обработки безопасных локальных подключений.</span><span class="sxs-lookup"><span data-stu-id="d7921-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="d7921-129">Дополнительные сведения см. в разделе [Конфигурация HTTPS](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="d7921-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="d7921-130">Удалите `https://localhost:5001` (при его наличии) из свойства `applicationUrl` в файле *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d7921-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="d7921-131">Запустите [dotnet publish](/dotnet/core/tools/dotnet-publish) в среде разработки, чтобы упаковать приложение в каталог (например, *bin/Release/&lt;target_framework_moniker&gt;/publish*), который может выполняться на сервере:</span><span class="sxs-lookup"><span data-stu-id="d7921-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="d7921-132">Приложение может быть опубликовано как [автономное развертывание](/dotnet/core/deploying/#self-contained-deployments-scd), если вы предпочитаете не сохранять среду выполнения .NET Core на сервере.</span><span class="sxs-lookup"><span data-stu-id="d7921-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="d7921-133">Скопируйте приложение ASP.NET Core на сервер с помощью инструмента, интегрированного в ваш рабочий процесс (например, SCP или SFTP).</span><span class="sxs-lookup"><span data-stu-id="d7921-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="d7921-134">Обычно веб-приложения находятся в каталоге *var* (например, *var/www/helloapp*).</span><span class="sxs-lookup"><span data-stu-id="d7921-134">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="d7921-135">Если развертывание выполняется в рабочей среде, рабочий процесс непрерывной интеграции автоматически опубликует приложение и скопирует его ресурсы на сервер.</span><span class="sxs-lookup"><span data-stu-id="d7921-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="d7921-136">Проверьте работу приложения:</span><span class="sxs-lookup"><span data-stu-id="d7921-136">Test the app:</span></span>

1. <span data-ttu-id="d7921-137">Запустите приложение в командной строке: `dotnet <app_assembly>.dll`.</span><span class="sxs-lookup"><span data-stu-id="d7921-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="d7921-138">В браузере откройте страницу `http://<serveraddress>:<port>`, чтобы убедиться, что приложение локально работает на платформе Linux.</span><span class="sxs-lookup"><span data-stu-id="d7921-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="d7921-139">Настройка обратного прокси-сервера</span><span class="sxs-lookup"><span data-stu-id="d7921-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="d7921-140">Обратный прокси-сервер — это стандартный вариант настройки для обслуживания динамических веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="d7921-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="d7921-141">Обратный прокси-сервер завершает HTTP-запрос и перенаправляет его в приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7921-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="d7921-142">Использование обратного прокси-сервера</span><span class="sxs-lookup"><span data-stu-id="d7921-142">Use a reverse proxy server</span></span>

<span data-ttu-id="d7921-143">Kestrel является отличным решением для обслуживания динамического содержимого из ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7921-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="d7921-144">При этом компоненты для работы с веб-службами не настолько функциональны, как серверы типа IIS, Apache или Nginx.</span><span class="sxs-lookup"><span data-stu-id="d7921-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="d7921-145">Обратный прокси-сервер может облегчить такую работу, как обслуживание статического содержимого, кэширование и сжатие запросов, а также терминирование HTTPS с HTTP-сервера.</span><span class="sxs-lookup"><span data-stu-id="d7921-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="d7921-146">Обратный прокси-сервер можно разместить на отдельном компьютере или развернуть параллельно с HTTP-сервером.</span><span class="sxs-lookup"><span data-stu-id="d7921-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="d7921-147">В контексте данного руководства используется отдельный экземпляр Nginx.</span><span class="sxs-lookup"><span data-stu-id="d7921-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="d7921-148">Он выполняется на том же сервере, что и HTTP-сервер.</span><span class="sxs-lookup"><span data-stu-id="d7921-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="d7921-149">Настройки можно выбирать в зависимости от требований.</span><span class="sxs-lookup"><span data-stu-id="d7921-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="d7921-150">Так как запросы перенаправляются обратным прокси-сервером, используйте [ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer), которое входит в пакет [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/).</span><span class="sxs-lookup"><span data-stu-id="d7921-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="d7921-151">Это ПО обновляет `Request.Scheme`, используя заголовок `X-Forwarded-Proto`, что обеспечивает правильную работу URI перенаправления и других политик безопасности.</span><span class="sxs-lookup"><span data-stu-id="d7921-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>


[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="d7921-152">Вызовите <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> метод наверху `Startup.Configure`, прежде чем вызывать другое ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="d7921-152">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="d7921-153">В ПО промежуточного слоя настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto`:</span><span class="sxs-lookup"><span data-stu-id="d7921-153">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="d7921-154">Если параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не задан для ПО промежуточного слоя, по умолчанию перенаправляются заголовки `None`.</span><span class="sxs-lookup"><span data-stu-id="d7921-154">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="d7921-155">Прокси-серверы под управлением адресов замыкания на себя (127.0.0.0/8, [:: 1]), включая стандартные адреса localhost (127.0.0.1), считаются доверенными по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d7921-155">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="d7921-156">Если запросы между Интернетом и веб-сервером обрабатывают другие прокси-серверы или сети организации, добавьте их в список <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> или <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> с помощью <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="d7921-156">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="d7921-157">Следующий пример добавляет доверенный прокси-сервер с IP-адресом 10.0.0.100 в ПО промежуточного слоя для перенаправления заголовков `KnownProxies` в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d7921-157">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="d7921-158">Для получения дополнительной информации см. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="d7921-158">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="d7921-159">Установка Nginx</span><span class="sxs-lookup"><span data-stu-id="d7921-159">Install Nginx</span></span>

<span data-ttu-id="d7921-160">Установите Nginx с помощью команды `apt-get`.</span><span class="sxs-lookup"><span data-stu-id="d7921-160">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="d7921-161">Программа установки создает сценарий инициализации *systemd*, который запускает Nginx как управляющую программу при запуске системы.</span><span class="sxs-lookup"><span data-stu-id="d7921-161">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="d7921-162">Следуйте инструкциям по установке для Ubuntu в разделе [Nginx: официальные пакеты Debian и Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span><span class="sxs-lookup"><span data-stu-id="d7921-162">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="d7921-163">Если необходимы дополнительные модули Nginx, может потребоваться создание Nginx из источника.</span><span class="sxs-lookup"><span data-stu-id="d7921-163">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="d7921-164">Так как Nginx устанавливается впервые, запустите его напрямую, выполнив следующую команду.</span><span class="sxs-lookup"><span data-stu-id="d7921-164">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="d7921-165">В браузере должна открыться стартовая страница Nginx по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d7921-165">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="d7921-166">Целевая страница доступна по адресу `http://<server_IP_address>/index.nginx-debian.html`.</span><span class="sxs-lookup"><span data-stu-id="d7921-166">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="d7921-167">Настройка Nginx</span><span class="sxs-lookup"><span data-stu-id="d7921-167">Configure Nginx</span></span>

<span data-ttu-id="d7921-168">Чтобы настроить Nginx как обратный прокси-сервер для перенаправления запросов в наше приложение ASP.NET Core, измените файл */etc/nginx/sites-available/default*.</span><span class="sxs-lookup"><span data-stu-id="d7921-168">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default*.</span></span> <span data-ttu-id="d7921-169">Откройте этот файл в текстовом редакторе и замените его содержимое на следующий код.</span><span class="sxs-lookup"><span data-stu-id="d7921-169">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="d7921-170">Если это приложение Blazor Server, которое использует соединения WebSocket SignalR, см. сведения о том, как задать заголовок `Connection`, в разделе <xref:blazor/host-and-deploy/server#linux-with-nginx>.</span><span class="sxs-lookup"><span data-stu-id="d7921-170">If the app is a Blazor Server app that relies on SignalR WebSockets, see <xref:blazor/host-and-deploy/server#linux-with-nginx> for information on how to set the `Connection` header.</span></span>

<span data-ttu-id="d7921-171">Если отсутствуют совпадения для `server_name`, Nginx использует сервер по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d7921-171">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="d7921-172">Если сервер по умолчанию не определен, первый сервер в файле конфигурации является сервером по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d7921-172">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="d7921-173">Рекомендуется добавить в качестве сервера по умолчанию определенный сервер, который возвращает код состояния 444 в файле конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d7921-173">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="d7921-174">Ниже приведен пример конфигурации сервера по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="d7921-174">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="d7921-175">С представленным выше файлом конфигурации и сервером по умолчанию Nginx принимает трафик от любого источника через порт 80 с заголовком узла `example.com` или `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="d7921-175">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="d7921-176">Запросы, не соответствующие этим узлам, не будут перенаправляться в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d7921-176">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="d7921-177">Запросы, которые им соответствуют, Nginx перенаправляет в Kestrel по адресу `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="d7921-177">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="d7921-178">Для получения дополнительной информации см. статью [Как nginx обрабатывает запросы](https://nginx.org/docs/http/request_processing.html).</span><span class="sxs-lookup"><span data-stu-id="d7921-178">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="d7921-179">Сведения о том, как изменить IP-адрес/порт Kestrel, см. в разделе [Kestrel: конфигурация конечной точки](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="d7921-179">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="d7921-180">Если не будет указана правильная [директива server_name](https://nginx.org/docs/http/server_names.html), приложение будет подвержено значительным уязвимостям.</span><span class="sxs-lookup"><span data-stu-id="d7921-180">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="d7921-181">Привязки с подстановочными знаками на уровне дочерних доменов (например, `*.example.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость).</span><span class="sxs-lookup"><span data-stu-id="d7921-181">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d7921-182">Дополнительные сведения см. в документе [rfc7230, раздел 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="d7921-182">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="d7921-183">Установив конфигурацию Nginx, выполните команду `sudo nginx -t`, чтобы проверить синтаксис файлов конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d7921-183">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="d7921-184">Если проверка файла конфигурации прошла успешно, заставьте Nginx принять изменения, выполнив команду `sudo nginx -s reload`.</span><span class="sxs-lookup"><span data-stu-id="d7921-184">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="d7921-185">Для непосредственного запуска приложений на сервере:</span><span class="sxs-lookup"><span data-stu-id="d7921-185">To directly run the app on the server:</span></span>

1. <span data-ttu-id="d7921-186">Перейдите в каталог приложения.</span><span class="sxs-lookup"><span data-stu-id="d7921-186">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="d7921-187">Запустите приложение: `dotnet <app_assembly.dll>`, где `app_assembly.dll` — имя файла сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="d7921-187">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="d7921-188">Если приложение выполняется на сервере, но не отвечает по Интернету, проверьте брандмауэр сервера и убедитесь, что порт 80 открыт.</span><span class="sxs-lookup"><span data-stu-id="d7921-188">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="d7921-189">При использовании виртуальной машины Ubuntu Azure добавьте правило группы безопасности сети (NSG), которое разрешает входящий трафик через порт 80.</span><span class="sxs-lookup"><span data-stu-id="d7921-189">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="d7921-190">Не нужно включать правило исходящего трафика на порте 80, так как исходящий трафик предоставляется автоматически при включении правила для входящего трафика.</span><span class="sxs-lookup"><span data-stu-id="d7921-190">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="d7921-191">Когда закончите тестировать приложение, завершите его работу с помощью `Ctrl+C` в командной строке.</span><span class="sxs-lookup"><span data-stu-id="d7921-191">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="d7921-192">Мониторинг приложения</span><span class="sxs-lookup"><span data-stu-id="d7921-192">Monitor the app</span></span>

<span data-ttu-id="d7921-193">Сервер настроен на перенаправление запросов к `http://<serveraddress>:80` в приложение ASP.NET Core, выполняемое в Kestrel по адресу `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="d7921-193">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="d7921-194">При этом Nginx не настроен для управления процессом Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d7921-194">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="d7921-195">Для запуска и мониторинга соответствующего веб-приложения можно использовать *systemd* и создать файл службы.</span><span class="sxs-lookup"><span data-stu-id="d7921-195">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="d7921-196">*systemd* — это система инициализации, предоставляющая различные функции для запуска и остановки процессов, а также управления ими.</span><span class="sxs-lookup"><span data-stu-id="d7921-196">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="d7921-197">Создание файла службы</span><span class="sxs-lookup"><span data-stu-id="d7921-197">Create the service file</span></span>

<span data-ttu-id="d7921-198">Создайте файл определения службы.</span><span class="sxs-lookup"><span data-stu-id="d7921-198">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="d7921-199">Далее представлен пример файла службы для нашего приложения.</span><span class="sxs-lookup"><span data-stu-id="d7921-199">The following is an example service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="d7921-200">В предыдущем примере управляющий службой пользователь задается с помощью параметра `User`.</span><span class="sxs-lookup"><span data-stu-id="d7921-200">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="d7921-201">Этот пользователь (`www-data`) должен существовать и иметь права владельца в отношении файлов приложения.</span><span class="sxs-lookup"><span data-stu-id="d7921-201">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="d7921-202">Используйте `TimeoutStopSec`, чтобы настроить время ожидания до завершения работы приложения после начального сигнала прерывания.</span><span class="sxs-lookup"><span data-stu-id="d7921-202">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="d7921-203">Если приложение не завершит работу в течение этого периода, оно прерывается сигналом SIGKILL.</span><span class="sxs-lookup"><span data-stu-id="d7921-203">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="d7921-204">Укажите значение в секундах без единиц измерения (например, `150`), значение интервала (например, `2min 30s`) или значение `infinity`, которое отключает время ожидания.</span><span class="sxs-lookup"><span data-stu-id="d7921-204">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="d7921-205">По умолчанию `TimeoutStopSec` принимает значение `DefaultTimeoutStopSec` в файле конфигурации диспетчера (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="d7921-205">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="d7921-206">В большинстве дистрибутивов по умолчанию устанавливается время ожидания 90 секунд.</span><span class="sxs-lookup"><span data-stu-id="d7921-206">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="d7921-207">Linux имеет файловую систему, в которой учитывается регистр символов.</span><span class="sxs-lookup"><span data-stu-id="d7921-207">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="d7921-208">Если для параметра ASPNETCORE_ENVIRONMENT установить значение Production, будет выполнен поиск файла конфигурации *appsettings.Production.json*, а не файла *appsettings.production.json*.</span><span class="sxs-lookup"><span data-stu-id="d7921-208">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json*, not *appsettings.production.json*.</span></span>

<span data-ttu-id="d7921-209">Некоторые значения (например, строки подключения SQL) необходимо экранировать, чтобы поставщики конфигурации могли читать переменные среды.</span><span class="sxs-lookup"><span data-stu-id="d7921-209">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="d7921-210">Используйте следующую команду, чтобы создать правильно экранированное значение для файла конфигурации:</span><span class="sxs-lookup"><span data-stu-id="d7921-210">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d7921-211">Разделители-двоеточия (`:`) не поддерживаются в именах переменных среды.</span><span class="sxs-lookup"><span data-stu-id="d7921-211">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="d7921-212">Следует использовать двойной знак подчеркивания (`__`) вместо двоеточия.</span><span class="sxs-lookup"><span data-stu-id="d7921-212">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="d7921-213">[Поставщик конфигурации переменных среды](xref:fundamentals/configuration/index#environment-variables) преобразует двойные символы подчеркивания в двоеточия, когда переменные среды считываются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d7921-213">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="d7921-214">В следующем примере ключ строки подключения `ConnectionStrings:DefaultConnection` задается в файле определения службы как `ConnectionStrings__DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="d7921-214">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d7921-215">Разделители-двоеточия (`:`) не поддерживаются в именах переменных среды.</span><span class="sxs-lookup"><span data-stu-id="d7921-215">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="d7921-216">Следует использовать двойной знак подчеркивания (`__`) вместо двоеточия.</span><span class="sxs-lookup"><span data-stu-id="d7921-216">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="d7921-217">[Поставщик конфигурации переменных среды](xref:fundamentals/configuration/index#environment-variables-configuration-provider) преобразует двойные символы подчеркивания в двоеточия, когда переменные среды считываются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d7921-217">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="d7921-218">В следующем примере ключ строки подключения `ConnectionStrings:DefaultConnection` задается в файле определения службы как `ConnectionStrings__DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="d7921-218">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="d7921-219">Сохраните файл и включите службу.</span><span class="sxs-lookup"><span data-stu-id="d7921-219">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="d7921-220">Запустите службу и убедитесь, что она работает.</span><span class="sxs-lookup"><span data-stu-id="d7921-220">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="d7921-221">Теперь, когда обратный прокси-сервер настроен и systemd управляет процессом Kestrel, веб-приложение можно считать полностью настроенным и вы можете обратиться к нему по адресу `http://localhost` из браузера на локальном компьютере.</span><span class="sxs-lookup"><span data-stu-id="d7921-221">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="d7921-222">Оно также доступно для удаленных компьютеров, несмотря на наличие блокирующих трафик межсетевых экранов.</span><span class="sxs-lookup"><span data-stu-id="d7921-222">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="d7921-223">Заголовок `Server` в ответе подтверждает, что приложение ASP.NET Core обслуживается Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d7921-223">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="d7921-224">Просмотр журналов</span><span class="sxs-lookup"><span data-stu-id="d7921-224">View logs</span></span>

<span data-ttu-id="d7921-225">Так как веб-приложение, использующее Kestrel, управляется через `systemd`, все события и процессы регистрируются в централизованном журнале.</span><span class="sxs-lookup"><span data-stu-id="d7921-225">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="d7921-226">При этом журнал содержит все записи обо всех службах и процессах, управляемых `systemd`.</span><span class="sxs-lookup"><span data-stu-id="d7921-226">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="d7921-227">Чтобы просмотреть элементы, связанные с `kestrel-helloapp.service`, используйте следующую команду.</span><span class="sxs-lookup"><span data-stu-id="d7921-227">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="d7921-228">Кроме того, количество возвращаемых записей можно уменьшить, указав параметры времени, например `--since today`, `--until 1 hour ago` или их комбинацию.</span><span class="sxs-lookup"><span data-stu-id="d7921-228">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="d7921-229">Защита данных</span><span class="sxs-lookup"><span data-stu-id="d7921-229">Data protection</span></span>

<span data-ttu-id="d7921-230">[Стек защиты данных в ASP.NET Core](xref:security/data-protection/introduction) используется определенным [ПО промежуточного слоя](xref:fundamentals/middleware/index) ASP.NET Core, включая промежуточное ПО для проверки подлинности (например, промежуточное ПО файлов cookie) и средствами защиты от подделки межсайтовых запросов.</span><span class="sxs-lookup"><span data-stu-id="d7921-230">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="d7921-231">Даже если API-интерфейсы защиты данных не вызываются из пользовательского кода, необходимо настроить защиту данных для создания постоянного [хранилища криптографических ключей](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="d7921-231">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="d7921-232">Если защита данных не настроена, ключи хранятся в памяти и удаляются при перезапуске приложения.</span><span class="sxs-lookup"><span data-stu-id="d7921-232">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="d7921-233">Если набор ключей хранится в памяти, при перезапуске приложения происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="d7921-233">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="d7921-234">Все токены аутентификации, использующие файлы cookie, становятся недействительными.</span><span class="sxs-lookup"><span data-stu-id="d7921-234">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="d7921-235">При выполнении следующего запроса пользователю требуется выполнить вход снова.</span><span class="sxs-lookup"><span data-stu-id="d7921-235">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="d7921-236">Все данные, защищенные с помощью набора ключей, больше не могут быть расшифрованы.</span><span class="sxs-lookup"><span data-stu-id="d7921-236">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="d7921-237">Это могут быть [токены CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) и [файлы cookie временных данных ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="d7921-237">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="d7921-238">Сведения о настройке защиты данных для хранения и шифрования набора ключей см. в приведенных ниже статьях.</span><span class="sxs-lookup"><span data-stu-id="d7921-238">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="d7921-239">Длинные поля заголовка запроса</span><span class="sxs-lookup"><span data-stu-id="d7921-239">Long request header fields</span></span>

<span data-ttu-id="d7921-240">Параметры прокси-сервера по умолчанию обычно ограничивают длину полей заголовка запроса значением 4 КБ или 8 КБ (в зависимости от платформы).</span><span class="sxs-lookup"><span data-stu-id="d7921-240">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="d7921-241">Приложению могут потребоваться более длинные поля (например, приложениям, использующим [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span><span class="sxs-lookup"><span data-stu-id="d7921-241">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="d7921-242">В этом случае требуется скорректировать параметры по умолчанию для прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="d7921-242">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="d7921-243">Применяемые значения зависят от конкретного сценария.</span><span class="sxs-lookup"><span data-stu-id="d7921-243">The values to apply depend on the scenario.</span></span> <span data-ttu-id="d7921-244">Дополнительные сведения см. в документации сервера.</span><span class="sxs-lookup"><span data-stu-id="d7921-244">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="d7921-245">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="d7921-245">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="d7921-246">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="d7921-246">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="d7921-247">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="d7921-247">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="d7921-248">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="d7921-248">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="d7921-249">Не увеличивайте значение буферов прокси-сервера по умолчанию, если это не требуется.</span><span class="sxs-lookup"><span data-stu-id="d7921-249">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="d7921-250">Увеличение этих значений повышает риск переполнения буфера и атак типа "отказ в обслуживании" (DoS) со стороны злоумышленников.</span><span class="sxs-lookup"><span data-stu-id="d7921-250">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="d7921-251">Защита приложения</span><span class="sxs-lookup"><span data-stu-id="d7921-251">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="d7921-252">Включение AppArmor</span><span class="sxs-lookup"><span data-stu-id="d7921-252">Enable AppArmor</span></span>

<span data-ttu-id="d7921-253">Начиная с версии 2.6 ядро Linux включает платформу модулей безопасности Linux (LSM).</span><span class="sxs-lookup"><span data-stu-id="d7921-253">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="d7921-254">LSM поддерживают различные реализации модулей безопасности.</span><span class="sxs-lookup"><span data-stu-id="d7921-254">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="d7921-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) — это LSM, который реализует систему обязательного контроля доступа, позволяющую ограничивать программу определенным набором ресурсов.</span><span class="sxs-lookup"><span data-stu-id="d7921-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="d7921-256">Убедитесь, что AppArmor включен и правильно настроен.</span><span class="sxs-lookup"><span data-stu-id="d7921-256">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="d7921-257">Настройка брандмауэра</span><span class="sxs-lookup"><span data-stu-id="d7921-257">Configure the firewall</span></span>

<span data-ttu-id="d7921-258">Закройте все внешние порты, которые не используются.</span><span class="sxs-lookup"><span data-stu-id="d7921-258">Close off all external ports that are not in use.</span></span> <span data-ttu-id="d7921-259">Простой брандмауэр (ufw) позволяет взаимодействовать с `iptables`. Для настройки брандмауэра предоставляется CLI.</span><span class="sxs-lookup"><span data-stu-id="d7921-259">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="d7921-260">Неправильно настроенный брандмауэр предотвратит доступ ко всей системе.</span><span class="sxs-lookup"><span data-stu-id="d7921-260">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="d7921-261">Если задать неправильный порт SSH, то при использовании SSH для подключения к системе произойдет ее блокировка.</span><span class="sxs-lookup"><span data-stu-id="d7921-261">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="d7921-262">Порт по умолчанию — 22.</span><span class="sxs-lookup"><span data-stu-id="d7921-262">The default port is 22.</span></span> <span data-ttu-id="d7921-263">Дополнительные сведения см. в [вводной статье по ufw](https://help.ubuntu.com/community/UFW) и в [этом руководстве](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="d7921-263">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="d7921-264">Установите `ufw` и настройте его для разрешения прохождения трафика через все необходимые порты.</span><span class="sxs-lookup"><span data-stu-id="d7921-264">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="d7921-265">Защита Nginx</span><span class="sxs-lookup"><span data-stu-id="d7921-265">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="d7921-266">Изменение имени ответа Nginx</span><span class="sxs-lookup"><span data-stu-id="d7921-266">Change the Nginx response name</span></span>

<span data-ttu-id="d7921-267">Внесите изменения в файл *src/http/ngx_http_header_filter_module.c*:</span><span class="sxs-lookup"><span data-stu-id="d7921-267">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="d7921-268">Настройка параметров</span><span class="sxs-lookup"><span data-stu-id="d7921-268">Configure options</span></span>

<span data-ttu-id="d7921-269">Настройте дополнительные обязательные модули на сервере.</span><span class="sxs-lookup"><span data-stu-id="d7921-269">Configure the server with additional required modules.</span></span> <span data-ttu-id="d7921-270">Для дополнительной защиты приложения можно использовать межсетевой экран для веб-приложений, например [ModSecurity](https://www.modsecurity.org/).</span><span class="sxs-lookup"><span data-stu-id="d7921-270">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="d7921-271">Конфигурация HTTPS</span><span class="sxs-lookup"><span data-stu-id="d7921-271">HTTPS configuration</span></span>

<span data-ttu-id="d7921-272">**Настройка приложения для безопасных (HTTPS) локальных подключений**</span><span class="sxs-lookup"><span data-stu-id="d7921-272">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="d7921-273">Команда [dotnet run](/dotnet/core/tools/dotnet-run) использует файл приложения *Properties/launchSettings.json*, который настраивает приложение для прослушивания URL-адресов, заданных свойством `applicationUrl` (например, `https://localhost:5001;http://localhost:5000`).</span><span class="sxs-lookup"><span data-stu-id="d7921-273">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="d7921-274">Настройте приложение для использования при разработке сертификата для команды `dotnet run` или среды разработки (F5 или CTRL + F5 в Visual Studio Code), используя один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="d7921-274">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="d7921-275">[Замена сертификата по умолчанию из конфигурации](xref:fundamentals/servers/kestrel#configuration) (*рекомендуется*)</span><span class="sxs-lookup"><span data-stu-id="d7921-275">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="d7921-276">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="d7921-276">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="d7921-277">**Настройка обратного прокси-сервера для безопасного подключения клиентов (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="d7921-277">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="d7921-278">Настройте сервер для прослушивания трафика HTTPS через порт `443`, указав действительный сертификат, выпущенный доверенным центром сертификации (ЦС).</span><span class="sxs-lookup"><span data-stu-id="d7921-278">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="d7921-279">Обеспечьте дополнительную защиту, применив некоторые методы, показанные в представленном ниже файле */etc/nginx/nginx.conf*.</span><span class="sxs-lookup"><span data-stu-id="d7921-279">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="d7921-280">Это может быть выбор более строгого шифра и перенаправление всего HTTP-трафика в HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d7921-280">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="d7921-281">При добавлении заголовка `HTTP Strict-Transport-Security` (HSTS) все последующие запросы клиента будут проходить по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d7921-281">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="d7921-282">Если протокол HTTPS будет отключен в будущем, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="d7921-282">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="d7921-283">Не добавляйте заголовок HSTS.</span><span class="sxs-lookup"><span data-stu-id="d7921-283">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="d7921-284">Выберите короткое значение `max-age`.</span><span class="sxs-lookup"><span data-stu-id="d7921-284">Choose a short `max-age` value.</span></span>

<span data-ttu-id="d7921-285">Добавьте файл конфигурации */etc/nginx/proxy.conf*:</span><span class="sxs-lookup"><span data-stu-id="d7921-285">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="d7921-286">Измените файл конфигурации */etc/nginx/proxy.conf*.</span><span class="sxs-lookup"><span data-stu-id="d7921-286">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="d7921-287">В этом примере показаны разделы `http` и `server` одного и того же файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d7921-287">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> <span data-ttu-id="d7921-288">Приложениям Blazor WebAssembly требуется большее значение параметра `burst` с учетом большего количества запросов, выполняемых приложением.</span><span class="sxs-lookup"><span data-stu-id="d7921-288">Blazor WebAssembly apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="d7921-289">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/webassembly#nginx>.</span><span class="sxs-lookup"><span data-stu-id="d7921-289">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="d7921-290">Защита Nginx от кликджекинга</span><span class="sxs-lookup"><span data-stu-id="d7921-290">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="d7921-291">[Кликджекинг](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger) (или *атака с подменой пользовательского интерфейса*) является вредоносной атакой, при которой посетителя сайта обманным путем вынуждают щелкнуть ссылку или нажать кнопку не той страницы, на которой он находится.</span><span class="sxs-lookup"><span data-stu-id="d7921-291">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="d7921-292">Используйте `X-FRAME-OPTIONS` для защиты сайта.</span><span class="sxs-lookup"><span data-stu-id="d7921-292">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="d7921-293">Чтобы уменьшить риск атак кликджекинга, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="d7921-293">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="d7921-294">Измените файл *nginx.conf*.</span><span class="sxs-lookup"><span data-stu-id="d7921-294">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="d7921-295">Добавьте строку `add_header X-Frame-Options "SAMEORIGIN";`.</span><span class="sxs-lookup"><span data-stu-id="d7921-295">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="d7921-296">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="d7921-296">Save the file.</span></span>
1. <span data-ttu-id="d7921-297">Перезапустите Nginx.</span><span class="sxs-lookup"><span data-stu-id="d7921-297">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="d7921-298">Сканирование типа MIME</span><span class="sxs-lookup"><span data-stu-id="d7921-298">MIME-type sniffing</span></span>

<span data-ttu-id="d7921-299">Этот заголовок предотвращает MIME-сканирование ответов с указанным типом содержимого в большинстве браузеров, запрещая браузеру переопределять тип содержимого ответа.</span><span class="sxs-lookup"><span data-stu-id="d7921-299">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="d7921-300">Параметр `nosniff` означает, что, если сервер определяет содержимое как text/html, браузер будет обрабатывать его как text/html.</span><span class="sxs-lookup"><span data-stu-id="d7921-300">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="d7921-301">Измените файл *nginx.conf*.</span><span class="sxs-lookup"><span data-stu-id="d7921-301">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="d7921-302">Добавьте строку `add_header X-Content-Type-Options "nosniff";` и сохраните файл, а затем перезапустите Nginx.</span><span class="sxs-lookup"><span data-stu-id="d7921-302">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="d7921-303">Дополнительные предложения Nginx</span><span class="sxs-lookup"><span data-stu-id="d7921-303">Additional Nginx suggestions</span></span>

<span data-ttu-id="d7921-304">После обновления общей платформы на сервере перезапустите размещенные на нем приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7921-304">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d7921-305">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d7921-305">Additional resources</span></span>

* [<span data-ttu-id="d7921-306">Необходимые компоненты для .NET Core в Linux</span><span class="sxs-lookup"><span data-stu-id="d7921-306">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="d7921-307">Nginx: двоичные выпуски: официальные пакеты Debian и Ubuntu</span><span class="sxs-lookup"><span data-stu-id="d7921-307">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="d7921-308">NGINX: использование перенаправленного заголовка</span><span class="sxs-lookup"><span data-stu-id="d7921-308">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
