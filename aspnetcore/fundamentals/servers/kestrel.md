---
title: Реализации веб-сервера Kestrel в ASP.NET Core
author: rick-anderson
description: Общие сведения о Kestrel — кроссплатформенном веб-сервере для ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/servers/kestrel
ms.openlocfilehash: fd0db90e897a813e0127ce1c751569158efa8fdf
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074168"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="f7077-103">Реализации веб-сервера Kestrel в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7077-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="f7077-104">Авторы: [Том Дикстра](https://github.com/tdykstra) (Tom Dykstra), [Крис Росс](https://github.com/Tratcher) (Chris Ross) и [Стивен Хальтер](https://twitter.com/halter73) (Stephen Halter)</span><span class="sxs-lookup"><span data-stu-id="f7077-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f7077-105">Kestrel — это кроссплатформенный [веб-сервер для ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="f7077-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="f7077-106">Веб-сервер Kestrel по умолчанию включается в шаблоны проектов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7077-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="f7077-107">Kestrel поддерживается в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="f7077-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="f7077-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="f7077-108">HTTPS</span></span>
* <span data-ttu-id="f7077-109">Непрозрачное обновление для поддержки [WebSocket](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="f7077-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="f7077-110">Сокеты UNIX для повышения производительности при работе за Nginx</span><span class="sxs-lookup"><span data-stu-id="f7077-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="f7077-111">HTTP/2 (за исключением macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="f7077-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="f7077-112">&dagger; HTTP/2 будет поддерживаться для macOS в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="f7077-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="f7077-113">Kestrel поддерживается на всех платформах и во всех версиях, поддерживаемых .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7077-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="f7077-114">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7077-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="f7077-115">Поддержка HTTP/2</span><span class="sxs-lookup"><span data-stu-id="f7077-115">HTTP/2 support</span></span>

<span data-ttu-id="f7077-116">Протокол [HTTP/2](https://httpwg.org/specs/rfc7540.html) доступен для приложений ASP.NET Core, если выполнены следующие базовые требования:</span><span class="sxs-lookup"><span data-stu-id="f7077-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="f7077-117">Операционная система&dagger;:</span><span class="sxs-lookup"><span data-stu-id="f7077-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="f7077-118">Windows Server 2016 / Windows 10 или более поздних версий&Dagger;</span><span class="sxs-lookup"><span data-stu-id="f7077-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="f7077-119">Linux с OpenSSL 1.0.2 или более поздней версии (например, Ubuntu 16.04 или более поздней версии).</span><span class="sxs-lookup"><span data-stu-id="f7077-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="f7077-120">Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней</span><span class="sxs-lookup"><span data-stu-id="f7077-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="f7077-121">Подключение с поддержкой [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3).</span><span class="sxs-lookup"><span data-stu-id="f7077-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="f7077-122">Подключение TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="f7077-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="f7077-123">&dagger; HTTP/2 будет поддерживаться для macOS в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="f7077-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="f7077-124">&Dagger;Для Kestrel предусмотрена ограниченная поддержка HTTP/2 в Windows Server 2012 R2 и Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="f7077-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="f7077-125">Поддержка ограничена из-за небольшого числа поддерживаемых комплектов шифров TLS, доступных для этих операционных систем.</span><span class="sxs-lookup"><span data-stu-id="f7077-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="f7077-126">Для обеспечения безопасности TLS-подключений может потребоваться сертификат, созданный с использованием алгоритма ECDSA.</span><span class="sxs-lookup"><span data-stu-id="f7077-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="f7077-127">Если установлено подключение HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="f7077-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="f7077-128">По умолчанию протокол HTTP/2 отключен.</span><span class="sxs-lookup"><span data-stu-id="f7077-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="f7077-129">Дополнительные сведения о конфигурации см. в разделах [о параметрах Kestrel](#kestrel-options) и [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="f7077-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="f7077-130">Условия использования Kestrel с обратным прокси-сервером</span><span class="sxs-lookup"><span data-stu-id="f7077-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="f7077-131">Kestrel можно использовать отдельно или с *обратным прокси-сервером*, таким как [IIS](https://www.iis.net/), [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f7077-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="f7077-132">Обратный прокси-сервер получает HTTP-запросы из сети и пересылает их в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="f7077-133">Kestrel используется в качестве веб-сервера перехода (с выходом в Интернет).</span><span class="sxs-lookup"><span data-stu-id="f7077-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel взаимодействует с Интернетом напрямую, без обратного прокси-сервера](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="f7077-135">Kestrel используется в конфигурации обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="f7077-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel взаимодействует с Интернетом косвенно, через обратный прокси-сервер, такой как IIS, Nginx или Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="f7077-137">Любая из этих конфигураций — с обратным прокси-сервером и без него — является поддерживаемой конфигурацией для размещения.</span><span class="sxs-lookup"><span data-stu-id="f7077-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="f7077-138">Kestrel, используемый в качестве пограничного сервера без обратного прокси-сервера, не поддерживает общий доступ нескольких процессов к одним и тем же IP-адресам и портам.</span><span class="sxs-lookup"><span data-stu-id="f7077-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="f7077-139">Когда Kestrel настроен на ожидание передачи данных от порта, Kestrel обрабатывает весь трафик для этого порта независимо от заголовка `Host` запросов.</span><span class="sxs-lookup"><span data-stu-id="f7077-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="f7077-140">Поэтому обратный прокси-сервер, который может совместно использовать порты, способен пересылать запросы в Kestrel с уникальным IP-адресом и портом.</span><span class="sxs-lookup"><span data-stu-id="f7077-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="f7077-141">Даже если обратный прокси-сервер не требуется, его использование может оказаться удобным.</span><span class="sxs-lookup"><span data-stu-id="f7077-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="f7077-142">Обратный прокси-сервер:</span><span class="sxs-lookup"><span data-stu-id="f7077-142">A reverse proxy:</span></span>

* <span data-ttu-id="f7077-143">Может ограничить общедоступную контактную зону размещенных на нем приложений.</span><span class="sxs-lookup"><span data-stu-id="f7077-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="f7077-144">Предоставляет дополнительный уровень конфигурации и защиты.</span><span class="sxs-lookup"><span data-stu-id="f7077-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="f7077-145">Может лучше интегрироваться с существующей инфраструктурой.</span><span class="sxs-lookup"><span data-stu-id="f7077-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="f7077-146">Упрощает настройку балансировки нагрузки и безопасных подключений (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f7077-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="f7077-147">Сертификат X.509 требуется только обратному прокси-серверу, а сам этот сервер может обмениваться данными с серверами приложения во внутренней сети по обычному протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7077-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="f7077-148">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="f7077-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="f7077-149">Kestrel в приложениях ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7077-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="f7077-150">Шаблоны проектов ASP.NET Core используют Kestrel по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="f7077-151">В *Program.cs* метод <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="f7077-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="f7077-152">Дополнительные сведения о создании узла см. в разделах *Настройка узла* и *Параметры построителя по умолчанию* статьи <xref:fundamentals/host/generic-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="f7077-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="f7077-153">Чтобы задать дополнительную конфигурацию после вызова `ConfigureWebHostDefaults`, используйте `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="f7077-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="f7077-154">Параметры Kestrel</span><span class="sxs-lookup"><span data-stu-id="f7077-154">Kestrel options</span></span>

<span data-ttu-id="f7077-155">Веб-сервер Kestrel имеет ограничительные параметры конфигурации, которые удобно использовать в развертываниях с выходом в Интернет.</span><span class="sxs-lookup"><span data-stu-id="f7077-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="f7077-156">Задать ограничения для свойства <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> в классе <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="f7077-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="f7077-157">Свойство `Limits` содержит экземпляр класса <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="f7077-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="f7077-158">В следующих примерах используется пространство имен <xref:Microsoft.AspNetCore.Server.Kestrel.Core>.</span><span class="sxs-lookup"><span data-stu-id="f7077-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="f7077-159">В примерах, приведенных далее в этой статье, параметры Kestrel настраиваются в коде C#.</span><span class="sxs-lookup"><span data-stu-id="f7077-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="f7077-160">Параметры Kestrel можно также задать с помощью [поставщика конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="f7077-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="f7077-161">Например, [поставщик конфигурации файла](xref:fundamentals/configuration/index#file-configuration-provider) может загрузить конфигурацию Kestrel из файла *appsettings.json* или *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="f7077-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="f7077-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> и [конфигурацию конечных точек](#endpoint-configuration) можно настроить из поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f7077-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="f7077-163">Оставшаяся конфигурация Kestrel должна настраиваться в коде C#.</span><span class="sxs-lookup"><span data-stu-id="f7077-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="f7077-164">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="f7077-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="f7077-165">Настройте Kestrel в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f7077-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="f7077-166">Внедрение экземпляра `IConfiguration` в класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="f7077-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="f7077-167">В следующем примере предполагается, что введенная конфигурация назначается свойству `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="f7077-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="f7077-168">В `Startup.ConfigureServices` загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel:</span><span class="sxs-lookup"><span data-stu-id="f7077-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="f7077-169">Настройте Kestrel при сборке узла:</span><span class="sxs-lookup"><span data-stu-id="f7077-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="f7077-170">В *Program.cs* загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="f7077-171">Оба предыдущих подхода работают с любым [поставщиком конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="f7077-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="f7077-172">Время ожидания проверки на активность</span><span class="sxs-lookup"><span data-stu-id="f7077-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="f7077-173">Получает или задает [время ожидания проверки на активность](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="f7077-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="f7077-174">Значение по умолчанию — 2 минуты.</span><span class="sxs-lookup"><span data-stu-id="f7077-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="f7077-175">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="f7077-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="f7077-176">Максимальное число одновременно открытых подключений TCP для всего приложения можно задать с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="f7077-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="f7077-177">Существует отдельный предел по подключениям, измененным с HTTP или HTTPS на другой протокол (например, по запросу WebSocket).</span><span class="sxs-lookup"><span data-stu-id="f7077-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="f7077-178">После изменения подключение не учитывается в пределе `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="f7077-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="f7077-179">По умолчанию максимальное число подключений не ограничено (null).</span><span class="sxs-lookup"><span data-stu-id="f7077-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="f7077-180">максимальный размер текста запроса;</span><span class="sxs-lookup"><span data-stu-id="f7077-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="f7077-181">По умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="f7077-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="f7077-182">Чтобы переопределить это ограничение в приложении MVC ASP.NET Core, мы рекомендуем использовать атрибут <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> в методе действия:</span><span class="sxs-lookup"><span data-stu-id="f7077-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="f7077-183">Приведенный ниже пример показывает, как настроить ограничение для приложения и каждого запроса:</span><span class="sxs-lookup"><span data-stu-id="f7077-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="f7077-184">Переопределите параметр для конкретного запроса в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="f7077-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="f7077-185">Если приложение пытается настроить ограничение для запроса после того, как оно начало считывать запрос, возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="f7077-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="f7077-186">Доступно свойство `IsReadOnly`, указывающее, что свойство `MaxRequestBodySize` находится в состоянии только для чтения и настраивать ограничение слишком поздно.</span><span class="sxs-lookup"><span data-stu-id="f7077-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="f7077-187">При запуске приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), который обслуживает [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module), ограничение на размер текста запроса Kestrel будет отключено, так как оно уже задается IIS.</span><span class="sxs-lookup"><span data-stu-id="f7077-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="f7077-188">минимальная скорость передачи данных в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="f7077-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="f7077-189">Kestrel каждую секунду проверяет, поступают ли данные с указанной скоростью в байтах в секунду.</span><span class="sxs-lookup"><span data-stu-id="f7077-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="f7077-190">Если скорость падает ниже минимума, для соединения истекает время ожидания. Льготный период — это количество времени, которое Kestrel дает клиенту на увеличение его скорости отправки до минимального уровня; в течение этого периода скорость не проверяется.</span><span class="sxs-lookup"><span data-stu-id="f7077-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="f7077-191">Льготный период помогает избежать разрыва соединений, которые первоначально отправляют данные с небольшой скоростью из-за медленного запуска TCP.</span><span class="sxs-lookup"><span data-stu-id="f7077-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="f7077-192">Минимальная скорость по умолчанию составляет 240 байт/с, льготный период равен 5 секундам.</span><span class="sxs-lookup"><span data-stu-id="f7077-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="f7077-193">Минимальная скорость также применяется к отклику.</span><span class="sxs-lookup"><span data-stu-id="f7077-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="f7077-194">Код для задания лимита запросов и лимита откликов различается только наличием `RequestBody` или `Response` в именах свойств и интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="f7077-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="f7077-195">Ниже приведен пример, показывающий, как настроить минимальную скорость передачи данных в *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f7077-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="f7077-196">Переопределите ограничения минимальной скорости для каждого запроса в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="f7077-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="f7077-197">Возможности <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>, указанные в предыдущем примере, отсутствуют в `HttpContext.Features` для запросов HTTP/2, так как изменение ограничений скорости для каждого запроса не поддерживается для HTTP/2 из-за поддержки протокола мультиплексирования запроса.</span><span class="sxs-lookup"><span data-stu-id="f7077-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="f7077-198">Но возможности <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> все еще присутствуют в `HttpContext.Features` для запросов HTTP/2, так как ограничение скорости чтения может быть *полностью отключено* для отдельных запросов. Чтобы сделать это, задайте для параметра `IHttpMinRequestBodyDataRateFeature.MinDataRate` значение `null` (даже для запроса HTTP/2).</span><span class="sxs-lookup"><span data-stu-id="f7077-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="f7077-199">При попытке чтения свойства `IHttpMinRequestBodyDataRateFeature.MinDataRate` или при попытке задать для него значение, отличное от `null`, возникнет исключение `NotSupportedException` для запроса HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7077-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="f7077-200">Ограничения скорости на уровне сервера, которые настроены с помощью `KestrelServerOptions.Limits`, по-прежнему применяются к подключениям HTTP/1.x и HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7077-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="f7077-201">Время ожидания для заголовков запросов</span><span class="sxs-lookup"><span data-stu-id="f7077-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="f7077-202">Получает или задает максимальное время, которое сервер уделяет получению заголовков запросов.</span><span class="sxs-lookup"><span data-stu-id="f7077-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="f7077-203">Значение по умолчанию — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="f7077-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="f7077-204">Максимальное число потоков на подключение</span><span class="sxs-lookup"><span data-stu-id="f7077-204">Maximum streams per connection</span></span>

<span data-ttu-id="f7077-205">`Http2.MaxStreamsPerConnection` ограничивает количество параллельных потоков запросов для одного соединения HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7077-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="f7077-206">Потоки сверх этого числа будут отклонены.</span><span class="sxs-lookup"><span data-stu-id="f7077-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="f7077-207">Значение по умолчанию — 100.</span><span class="sxs-lookup"><span data-stu-id="f7077-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="f7077-208">Размер таблицы заголовка</span><span class="sxs-lookup"><span data-stu-id="f7077-208">Header table size</span></span>

<span data-ttu-id="f7077-209">Декодер HPACK распаковывает заголовки HTTP для подключений HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7077-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="f7077-210">`Http2.HeaderTableSize` ограничивает размер таблицы сжатия заголовка, которую использует декодер HPACK.</span><span class="sxs-lookup"><span data-stu-id="f7077-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="f7077-211">Это значение указывается в октетах и должно быть больше нуля (0).</span><span class="sxs-lookup"><span data-stu-id="f7077-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="f7077-212">Значение по умолчанию — 4096.</span><span class="sxs-lookup"><span data-stu-id="f7077-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="f7077-213">Максимальный размер кадра</span><span class="sxs-lookup"><span data-stu-id="f7077-213">Maximum frame size</span></span>

<span data-ttu-id="f7077-214">`Http2.MaxFrameSize` указывает максимально допустимый размер полезных данных в кадре подключения HTTP/2, получаемых или отправляемых сервером.</span><span class="sxs-lookup"><span data-stu-id="f7077-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="f7077-215">Это значение указывается в октетах и должно находиться в пределах от 2^14 (16 384) до 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="f7077-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="f7077-216">Значение по умолчанию — 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="f7077-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="f7077-217">Максимальный размер запроса заголовка</span><span class="sxs-lookup"><span data-stu-id="f7077-217">Maximum request header size</span></span>

<span data-ttu-id="f7077-218">`Http2.MaxRequestHeaderFieldSize` указывает максимально допустимый размер значений заголовка запроса (в октетах).</span><span class="sxs-lookup"><span data-stu-id="f7077-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="f7077-219">Это ограничение применяется к имени и значению в их сжатых и несжатых представлениях.</span><span class="sxs-lookup"><span data-stu-id="f7077-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="f7077-220">Значение должно быть больше нуля.</span><span class="sxs-lookup"><span data-stu-id="f7077-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="f7077-221">Значение по умолчанию — 8 192.</span><span class="sxs-lookup"><span data-stu-id="f7077-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="f7077-222">Размер окна начального подключения</span><span class="sxs-lookup"><span data-stu-id="f7077-222">Initial connection window size</span></span>

<span data-ttu-id="f7077-223">`Http2.InitialConnectionWindowSize` указывает максимальное значение данных тела запроса (в байтах), буферизируемое сервером за один раз, для всех запросов (потоков) на каждое соединение.</span><span class="sxs-lookup"><span data-stu-id="f7077-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="f7077-224">Размеры запросов также ограничиваются параметром `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="f7077-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="f7077-225">Значение должно быть больше или равно 65 535 и меньше 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="f7077-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="f7077-226">Значение по умолчанию — 128 КБ (131 072).</span><span class="sxs-lookup"><span data-stu-id="f7077-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="f7077-227">Размер окна начального потока</span><span class="sxs-lookup"><span data-stu-id="f7077-227">Initial stream window size</span></span>

<span data-ttu-id="f7077-228">`Http2.InitialStreamWindowSize` указывает максимальное значение данных тела запроса (в байтах), буферизируемое сервером за один раз, для каждого запроса (потока).</span><span class="sxs-lookup"><span data-stu-id="f7077-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="f7077-229">Размеры запросов также ограничиваются параметром `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="f7077-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="f7077-230">Значение должно быть больше или равно 65 535 и меньше 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="f7077-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="f7077-231">Значение по умолчанию — 96 КБ (98 304).</span><span class="sxs-lookup"><span data-stu-id="f7077-231">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="f7077-232">Синхронный ввод-вывод</span><span class="sxs-lookup"><span data-stu-id="f7077-232">Synchronous I/O</span></span>

<span data-ttu-id="f7077-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> определяет, разрешены ли синхронные операции ввода-вывода для запроса и ответа.</span><span class="sxs-lookup"><span data-stu-id="f7077-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="f7077-234">Значение по умолчанию — `false`.</span><span class="sxs-lookup"><span data-stu-id="f7077-234">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="f7077-235">Выполнение большого числа заблокированных операций синхронного ввода-вывода может привести к перегрузке пула потоков и зависанию приложения.</span><span class="sxs-lookup"><span data-stu-id="f7077-235">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="f7077-236">Включайте `AllowSynchronousIO`, только если вы используете библиотеку, которая не поддерживает асинхронные операции ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="f7077-236">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="f7077-237">В примере ниже включаются синхронные операции ввода-вывода:</span><span class="sxs-lookup"><span data-stu-id="f7077-237">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="f7077-238">Сведения о других параметрах и ограничениях Kestrel см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="f7077-238">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="f7077-239">Конфигурация конечной точки</span><span class="sxs-lookup"><span data-stu-id="f7077-239">Endpoint configuration</span></span>

<span data-ttu-id="f7077-240">По умолчанию платформа ASP.NET Core привязана к:</span><span class="sxs-lookup"><span data-stu-id="f7077-240">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="f7077-241">`https://localhost:5001` (если присутствует локальный сертификат разработки)</span><span class="sxs-lookup"><span data-stu-id="f7077-241">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="f7077-242">Укажите URL-адреса с помощью следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="f7077-242">Specify URLs using the:</span></span>

* <span data-ttu-id="f7077-243">Переменная среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="f7077-243">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="f7077-244">Аргументы командной строки `--urls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-244">`--urls` command-line argument.</span></span>
* <span data-ttu-id="f7077-245">Ключ конфигурации узла `urls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-245">`urls` host configuration key.</span></span>
* <span data-ttu-id="f7077-246">Метод расширения `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-246">`UseUrls` extension method.</span></span>

<span data-ttu-id="f7077-247">Значение, указанное с помощью этих подходов, может быть одной или несколькими конечными точками HTTP и HTTPS (HTTPS при наличии сертификата по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="f7077-247">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="f7077-248">Настройте значение в виде списка с разделением точкой с запятой (например, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="f7077-248">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="f7077-249">Дополнительные сведения о таких подходах см. в разделах [URL-адреса сервера](xref:fundamentals/host/web-host#server-urls) и [Переопределение конфигурации](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="f7077-249">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="f7077-250">Сертификат разработки создается, когда:</span><span class="sxs-lookup"><span data-stu-id="f7077-250">A development certificate is created:</span></span>

* <span data-ttu-id="f7077-251">установлен [пакет SDK для .NET Core](/dotnet/core/sdk);</span><span class="sxs-lookup"><span data-stu-id="f7077-251">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="f7077-252">используется [средство dev-certs](xref:aspnetcore-2.1#https) для создания сертификата.</span><span class="sxs-lookup"><span data-stu-id="f7077-252">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="f7077-253">В некоторых браузерах требуется явное разрешение доверять локальному сертификату разработки.</span><span class="sxs-lookup"><span data-stu-id="f7077-253">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="f7077-254">Шаблоны проектов настраивают приложения так, чтобы они запускались на базе HTTPS по умолчанию и включали [поддержку перенаправления HTTPS и HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="f7077-254">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="f7077-255">Вызовите методы <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> или <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> из <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>, чтобы настроить префиксы URL-адресов и порты для Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-255">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="f7077-256">`UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` и переменная среды `ASPNETCORE_URLS` тоже работают, однако на них распространяются ограничения, указанные далее в этой статье (для конфигурации конечной точки HTTPS требуется сертификат по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="f7077-256">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="f7077-257">Конфигурация `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="f7077-257">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="f7077-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="f7077-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="f7077-259">Указывает конфигурацию `Action` для выполнения каждой заданной конечной точки.</span><span class="sxs-lookup"><span data-stu-id="f7077-259">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="f7077-260">Если вызвать `ConfigureEndpointDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.</span><span class="sxs-lookup"><span data-stu-id="f7077-260">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="f7077-261">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-261">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="f7077-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="f7077-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="f7077-263">Указывает конфигурацию `Action` для выполнения каждой конечной точки HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f7077-263">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="f7077-264">Если вызвать `ConfigureHttpsDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.</span><span class="sxs-lookup"><span data-stu-id="f7077-264">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="f7077-265">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="f7077-266">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="f7077-266">Configure(IConfiguration)</span></span>

<span data-ttu-id="f7077-267">Создает загрузчик конфигурации для настройки Kestrel, который принимает <xref:Microsoft.Extensions.Configuration.IConfiguration> в качестве входных данных.</span><span class="sxs-lookup"><span data-stu-id="f7077-267">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="f7077-268">Для Kestrel конфигурация должна быть ограничена разделом конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f7077-268">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="f7077-269">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="f7077-269">ListenOptions.UseHttps</span></span>

<span data-ttu-id="f7077-270">Настройте Kestrel для использования протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f7077-270">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="f7077-271">Расширения `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="f7077-271">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="f7077-272">`UseHttps`. Настройте Kestrel для использования протокола HTTPS с сертификатом по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-272">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="f7077-273">Создает исключение, если сертификат по умолчанию не настроен.</span><span class="sxs-lookup"><span data-stu-id="f7077-273">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="f7077-274">Параметры `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="f7077-274">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="f7077-275">`filename` — это путь и имя файла сертификата, связанного с каталогом, где находятся файлы содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="f7077-275">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="f7077-276">`password` — это пароль для доступа к данным сертификата X.509.</span><span class="sxs-lookup"><span data-stu-id="f7077-276">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="f7077-277">`configureOptions` — это `Action` для настройки `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="f7077-277">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="f7077-278">Возвращает `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="f7077-278">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="f7077-279">`storeName` — это хранилище сертификатов, из которого выполняется загрузка сертификата.</span><span class="sxs-lookup"><span data-stu-id="f7077-279">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="f7077-280">`subject` — это имя субъекта для сертификата.</span><span class="sxs-lookup"><span data-stu-id="f7077-280">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="f7077-281">`allowInvalid` указывает, следует ли учитывать недопустимые сертификаты, например самозаверяющие сертификаты.</span><span class="sxs-lookup"><span data-stu-id="f7077-281">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="f7077-282">`location` — это расположение хранилища, из которого загружается сертификат.</span><span class="sxs-lookup"><span data-stu-id="f7077-282">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="f7077-283">`serverCertificate` — это сертификат X.509.</span><span class="sxs-lookup"><span data-stu-id="f7077-283">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="f7077-284">В рабочей среде необходимо явно настроить HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f7077-284">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="f7077-285">Как минимум необходимо указать сертификат по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-285">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="f7077-286">Поддерживаемые конфигурации, описанные далее:</span><span class="sxs-lookup"><span data-stu-id="f7077-286">Supported configurations described next:</span></span>

* <span data-ttu-id="f7077-287">Отсутствие конфигурации</span><span class="sxs-lookup"><span data-stu-id="f7077-287">No configuration</span></span>
* <span data-ttu-id="f7077-288">Замена сертификата по умолчанию из конфигурации</span><span class="sxs-lookup"><span data-stu-id="f7077-288">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="f7077-289">Изменение значений по умолчанию в коде</span><span class="sxs-lookup"><span data-stu-id="f7077-289">Change the defaults in code</span></span>

<span data-ttu-id="f7077-290">*Отсутствие конфигурации*</span><span class="sxs-lookup"><span data-stu-id="f7077-290">*No configuration*</span></span>

<span data-ttu-id="f7077-291">Kestrel ожидает передачи данных через `http://localhost:5000` и `https://localhost:5001` (если доступен сертификат по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="f7077-291">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="f7077-292">*Замена сертификата по умолчанию из конфигурации*</span><span class="sxs-lookup"><span data-stu-id="f7077-292">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="f7077-293">`CreateDefaultBuilder` по умолчанию вызывает `Configure(context.Configuration.GetSection("Kestrel"))`, чтобы загрузить конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-293">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="f7077-294">Kestrel имеет доступ к схеме конфигурации параметров приложения HTTPS по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-294">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="f7077-295">Настройте несколько конечных точек, включая URL-адреса и сертификаты для использования, либо из файла на диске, либо из хранилища сертификатов.</span><span class="sxs-lookup"><span data-stu-id="f7077-295">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="f7077-296">В следующем примере *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f7077-296">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="f7077-297">Установите для **AllowInvalid** значение `true`, чтобы разрешить использование недопустимых сертификатов (например, самозаверяющих сертификатов).</span><span class="sxs-lookup"><span data-stu-id="f7077-297">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="f7077-298">Любая конечная точка HTTPS, которая не указывает сертификат (**HttpsDefaultCert** в следующем примере), будет использовать сертификат, определенный в разделе **Certificates** (Сертификаты) > **Default** (По умолчанию), или сертификат разработки.</span><span class="sxs-lookup"><span data-stu-id="f7077-298">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="f7077-299">Вместо использования параметров **Path** и **Password** для узла сертификата можно указать сертификат с помощью полей хранилища сертификатов.</span><span class="sxs-lookup"><span data-stu-id="f7077-299">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="f7077-300">Например, сертификат из раздела **Сертификаты** > **По умолчанию** можно указать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f7077-300">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="f7077-301">Примечания к схеме.</span><span class="sxs-lookup"><span data-stu-id="f7077-301">Schema notes:</span></span>

* <span data-ttu-id="f7077-302">Регистр букв в именах конечных точек не учитывается.</span><span class="sxs-lookup"><span data-stu-id="f7077-302">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="f7077-303">Например, `HTTPS` и `Https` являются допустимыми.</span><span class="sxs-lookup"><span data-stu-id="f7077-303">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="f7077-304">Параметр `Url` является обязательным для каждой конечной точки.</span><span class="sxs-lookup"><span data-stu-id="f7077-304">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="f7077-305">Формат этого параметра такой же, как для параметра конфигурации `Urls` верхнего уровня, только он ограничен одиночным значением.</span><span class="sxs-lookup"><span data-stu-id="f7077-305">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="f7077-306">Эти конечные точки заменяют конечные точки, определенные в конфигурации `Urls` верхнего уровня, а не дополняют их.</span><span class="sxs-lookup"><span data-stu-id="f7077-306">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="f7077-307">Конечные точки, определенные в коде через `Listen`, объединяются с конечными точками, определенными в разделе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f7077-307">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="f7077-308">Раздел `Certificate` является необязательным.</span><span class="sxs-lookup"><span data-stu-id="f7077-308">The `Certificate` section is optional.</span></span> <span data-ttu-id="f7077-309">Если раздел `Certificate` не указан, используются значения по умолчанию, определенные в предыдущих сценариях.</span><span class="sxs-lookup"><span data-stu-id="f7077-309">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="f7077-310">Если значений по умолчанию нет, сервер выдает исключение и не запускается.</span><span class="sxs-lookup"><span data-stu-id="f7077-310">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="f7077-311">Раздел `Certificate` поддерживает сертификаты **Path**&ndash;**Password** и **Subject**&ndash;**Store**.</span><span class="sxs-lookup"><span data-stu-id="f7077-311">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="f7077-312">Таким образом, можно определить любое количество конечных точек, если это не приводит к конфликту портов.</span><span class="sxs-lookup"><span data-stu-id="f7077-312">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="f7077-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` возвращает `KestrelConfigurationLoader` с методом `.Endpoint(string name, listenOptions => { })`, который может использоваться в качестве дополнения для параметров настроенной конечной точки:</span><span class="sxs-lookup"><span data-stu-id="f7077-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="f7077-314">Можно обратиться напрямую к `KestrelServerOptions.ConfigurationLoader`, чтобы и далее выполнять итерацию с существующим загрузчиком, например, предоставленным <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="f7077-314">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="f7077-315">Раздел конфигурации для каждой конечной точки доступен в параметрах в методе `Endpoint`, чтобы можно было прочитать пользовательские параметры.</span><span class="sxs-lookup"><span data-stu-id="f7077-315">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="f7077-316">Можно загрузить несколько конфигураций, снова вызвав `options.Configure(context.Configuration.GetSection("{SECTION}"))` с другим разделом.</span><span class="sxs-lookup"><span data-stu-id="f7077-316">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="f7077-317">Используется только последняя конфигурация, если явным образом не вызвать `Load` в предыдущих экземплярах.</span><span class="sxs-lookup"><span data-stu-id="f7077-317">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="f7077-318">Метапакет не вызывает `Load`, чтобы можно было заменить его раздел конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-318">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="f7077-319">`KestrelConfigurationLoader` отражает семейство API `Listen` из `KestrelServerOptions` как перегрузки `Endpoint`, чтобы можно было настроить конечные точки кода и конфигурации в одном месте.</span><span class="sxs-lookup"><span data-stu-id="f7077-319">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="f7077-320">Эти перегрузки не используют имена и используют только параметры по умолчанию из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f7077-320">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="f7077-321">*Изменение значений по умолчанию в коде*</span><span class="sxs-lookup"><span data-stu-id="f7077-321">*Change the defaults in code*</span></span>

<span data-ttu-id="f7077-322">Можно использовать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults` для изменения параметров по умолчанию для `ListenOptions` и `HttpsConnectionAdapterOptions`, включая переопределение сертификата по умолчанию, указанного в предыдущем сценарии.</span><span class="sxs-lookup"><span data-stu-id="f7077-322">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="f7077-323">Необходимо вызвать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults`, прежде чем настраивать конечные точки.</span><span class="sxs-lookup"><span data-stu-id="f7077-323">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="f7077-324">*Поддержка Kestrel для SNI*</span><span class="sxs-lookup"><span data-stu-id="f7077-324">*Kestrel support for SNI*</span></span>

<span data-ttu-id="f7077-325">Можно использовать [указание имени сервера (SNI)](https://tools.ietf.org/html/rfc6066#section-3) для размещения нескольких доменов в одном IP-адресе и порте.</span><span class="sxs-lookup"><span data-stu-id="f7077-325">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="f7077-326">Для использования SNI клиент отправляет имя узла для безопасного сеанса серверу во время подтверждения TLS, чтобы сервер предоставил правильный сертификат.</span><span class="sxs-lookup"><span data-stu-id="f7077-326">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="f7077-327">Клиент использует предоставленный сертификат для зашифрованного соединения с сервером во время безопасного сеанса, который следует после подтверждения TLS.</span><span class="sxs-lookup"><span data-stu-id="f7077-327">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="f7077-328">Kestrel поддерживает SNI через обратный вызов `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="f7077-328">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="f7077-329">Функция обратного вызова используется один раз за подключение, чтобы приложение проверило имя узла и выбрало соответствующий сертификат.</span><span class="sxs-lookup"><span data-stu-id="f7077-329">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="f7077-330">Поддержка SNI требует:</span><span class="sxs-lookup"><span data-stu-id="f7077-330">SNI support requires:</span></span>

* <span data-ttu-id="f7077-331">Запуск на целевой платформе `netcoreapp2.1` или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="f7077-331">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="f7077-332">В `net461` или более поздней версии обратный вызов выполняется, но `name` всегда имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="f7077-332">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="f7077-333">`name` также имеет значение `null`, если клиент не предоставляет параметр имени узла при подтверждении TLS.</span><span class="sxs-lookup"><span data-stu-id="f7077-333">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="f7077-334">Все веб-сайты выполняются на одном и том же экземпляре Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-334">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="f7077-335">Kestrel не поддерживает совместное использование IP-адреса и порта на нескольких экземплярах без обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="f7077-335">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="f7077-336">Ведение журнала подключения</span><span class="sxs-lookup"><span data-stu-id="f7077-336">Connection logging</span></span>

<span data-ttu-id="f7077-337">Вызовите <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>, чтобы выдать журналы уровня отладки для обмена данными на уровне байтов в рамках подключения.</span><span class="sxs-lookup"><span data-stu-id="f7077-337">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="f7077-338">Ведение журнала подключения полезно для устранения неполадок, связанных с низкоуровневым взаимодействием, например при TLS-шифровании и работе за прокси-серверами.</span><span class="sxs-lookup"><span data-stu-id="f7077-338">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="f7077-339">Если `UseConnectionLogging` поместить перед `UseHttps`, в журнале регистрируется зашифрованный трафик.</span><span class="sxs-lookup"><span data-stu-id="f7077-339">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="f7077-340">Если `UseConnectionLogging` поместить после `UseHttps`, в журнале регистрируется расшифрованный трафик.</span><span class="sxs-lookup"><span data-stu-id="f7077-340">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="f7077-341">Привязка к TCP-сокету</span><span class="sxs-lookup"><span data-stu-id="f7077-341">Bind to a TCP socket</span></span>

<span data-ttu-id="f7077-342">Метод <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> выполняет привязку к TCP-сокету, а лямбда-выражение параметров позволяет настроить конфигурацию сертификата X.509:</span><span class="sxs-lookup"><span data-stu-id="f7077-342">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="f7077-343">В этом примере настраивается HTTPS для конечной точки с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="f7077-343">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="f7077-344">С помощью этого API можно настроить и другие параметры Kestrel для отдельных конечных точек.</span><span class="sxs-lookup"><span data-stu-id="f7077-344">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="f7077-345">Привязка к сокету UNIX</span><span class="sxs-lookup"><span data-stu-id="f7077-345">Bind to a Unix socket</span></span>

<span data-ttu-id="f7077-346">Вы можете прослушивать сокет UNIX с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, чтобы улучшить производительность Nginx, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="f7077-346">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="f7077-347">В файле конфигурации Nginx установите для записи `server` > `location` > `proxy_pass` значение `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="f7077-347">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="f7077-348">`{KESTREL SOCKET}` — это имя сокета, предоставленного для <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (как `kestrel-test.sock` в предыдущем примере).</span><span class="sxs-lookup"><span data-stu-id="f7077-348">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="f7077-349">Убедитесь, что сокет доступен для записи Nginx (например, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="f7077-349">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="f7077-350">Порт 0</span><span class="sxs-lookup"><span data-stu-id="f7077-350">Port 0</span></span>

<span data-ttu-id="f7077-351">Если указать номер порта `0`, Kestrel динамически привязывается к доступному порту.</span><span class="sxs-lookup"><span data-stu-id="f7077-351">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="f7077-352">Следующий пример показывает, как определить, к какому порту фактически привязан Kestrel во время выполнения:</span><span class="sxs-lookup"><span data-stu-id="f7077-352">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="f7077-353">Когда приложение выполняется, в выходных данных в окне консоли указывается динамический порт, по которому можно связаться с приложением:</span><span class="sxs-lookup"><span data-stu-id="f7077-353">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="f7077-354">Ограничения</span><span class="sxs-lookup"><span data-stu-id="f7077-354">Limitations</span></span>

<span data-ttu-id="f7077-355">Настройте конечные точки с помощью следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="f7077-355">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="f7077-356">Аргументы командной строки `--urls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-356">`--urls` command-line argument</span></span>
* <span data-ttu-id="f7077-357">Ключ конфигурации узла `urls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-357">`urls` host configuration key</span></span>
* <span data-ttu-id="f7077-358">Переменная среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="f7077-358">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="f7077-359">Эти методы удобны, если нужно, чтобы код работал с серверами, отличными от Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-359">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="f7077-360">Не забывайте о следующих ограничениях.</span><span class="sxs-lookup"><span data-stu-id="f7077-360">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="f7077-361">С этими подходами нельзя использовать HTTPS, если в конфигурации конечной точки HTTPS не предоставлен сертификат по умолчанию (например, с помощью конфигурации `KestrelServerOptions` или файла конфигурации, как показано выше в этом разделе).</span><span class="sxs-lookup"><span data-stu-id="f7077-361">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="f7077-362">Если подходы `Listen` и `UseUrls` используются одновременно, конечные точки `Listen` переопределяют конечные точки `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-362">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="f7077-363">Конфигурация конечной точки IIS</span><span class="sxs-lookup"><span data-stu-id="f7077-363">IIS endpoint configuration</span></span>

<span data-ttu-id="f7077-364">При использовании служб IIS привязки URL-адресов для IIS переопределяют привязки, заданные `Listen` или `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-364">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="f7077-365">Дополнительные сведения см. в статье [Модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="f7077-365">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="f7077-366">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="f7077-366">ListenOptions.Protocols</span></span>

<span data-ttu-id="f7077-367">Свойство `Protocols` устанавливает протоколы HTTP (`HttpProtocols`), разрешенные для конечной точки подключения или для сервера.</span><span class="sxs-lookup"><span data-stu-id="f7077-367">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="f7077-368">Значение свойства `Protocols` должно входить в перечисление `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="f7077-368">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="f7077-369">Значение перечисления `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="f7077-369">`HttpProtocols` enum value</span></span> | <span data-ttu-id="f7077-370">Допустимый протокол подключения</span><span class="sxs-lookup"><span data-stu-id="f7077-370">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="f7077-371">Только HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="f7077-371">HTTP/1.1 only.</span></span> <span data-ttu-id="f7077-372">Можно использовать с протоколом TLS или без него.</span><span class="sxs-lookup"><span data-stu-id="f7077-372">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="f7077-373">Только HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7077-373">HTTP/2 only.</span></span> <span data-ttu-id="f7077-374">Может использоваться без TLS только в том случае, если клиент поддерживает [режим предварительного знания](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="f7077-374">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="f7077-375">HTTP/1.1 и HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7077-375">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="f7077-376">Для использования HTTP/2 требуется, чтобы клиент выбрал HTTP/2 при подтверждении [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3); в противном случае используется подключение по умолчанию HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="f7077-376">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="f7077-377">Значение `ListenOptions.Protocols` по умолчанию для любой конечной точки равно `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="f7077-377">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="f7077-378">Ограничения TLS для HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="f7077-378">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="f7077-379">TSL 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="f7077-379">TLS version 1.2 or later</span></span>
* <span data-ttu-id="f7077-380">Повторное согласование отключено.</span><span class="sxs-lookup"><span data-stu-id="f7077-380">Renegotiation disabled</span></span>
* <span data-ttu-id="f7077-381">Сжатие отключено.</span><span class="sxs-lookup"><span data-stu-id="f7077-381">Compression disabled</span></span>
* <span data-ttu-id="f7077-382">Минимальные размеры обмена временными ключами:</span><span class="sxs-lookup"><span data-stu-id="f7077-382">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="f7077-383">эллиптическая кривая Диффи—Хелмана (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: не менее 224 бит;</span><span class="sxs-lookup"><span data-stu-id="f7077-383">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="f7077-384">конечное поле Диффи—Хелмана (DHE) &lbrack;`TLS12`&rbrack;: не менее 2048 бит.</span><span class="sxs-lookup"><span data-stu-id="f7077-384">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="f7077-385">Набор шифров не запрещен.</span><span class="sxs-lookup"><span data-stu-id="f7077-385">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="f7077-386">По умолчанию поддерживается `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; с эллиптической кривой P-256 &lbrack;`FIPS186`&rbrack;.</span><span class="sxs-lookup"><span data-stu-id="f7077-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="f7077-387">Следующий пример разрешает подключения HTTP/1.1 и HTTP/2 через порт 8000.</span><span class="sxs-lookup"><span data-stu-id="f7077-387">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="f7077-388">Эти подключения шифруются по протоколу TLS с использованием предоставленного сертификата:</span><span class="sxs-lookup"><span data-stu-id="f7077-388">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="f7077-389">При необходимости, используйте ПО промежуточного слоя подключения для фильтрации подтверждений TLS для каждого соединения по конкретным шифрам.</span><span class="sxs-lookup"><span data-stu-id="f7077-389">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="f7077-390">Следующий пример вызывает <xref:System.NotSupportedException> для любого алгоритма шифрования, который не поддерживается приложением.</span><span class="sxs-lookup"><span data-stu-id="f7077-390">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="f7077-391">Также можно определить и сравнить [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) со списком приемлемых наборов шифров.</span><span class="sxs-lookup"><span data-stu-id="f7077-391">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="f7077-392">При использовании алгоритма шифрования [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) шифрование не используется.</span><span class="sxs-lookup"><span data-stu-id="f7077-392">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="f7077-393">Фильтрацию соединений также можно настроить с помощью лямбды <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>:</span><span class="sxs-lookup"><span data-stu-id="f7077-393">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="f7077-394">В Linux для фильтрации подтверждений TLS по каждому соединению можно использовать <xref:System.Net.Security.CipherSuitesPolicy>:</span><span class="sxs-lookup"><span data-stu-id="f7077-394">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="f7077-395">*Выбор протокола из конфигурации*</span><span class="sxs-lookup"><span data-stu-id="f7077-395">*Set the protocol from configuration*</span></span>

<span data-ttu-id="f7077-396">`CreateDefaultBuilder` по умолчанию вызывает `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, чтобы загрузить конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-396">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="f7077-397">В следующем примере *appsettings.json* для всех конечных точек устанавливается протокол подключения по умолчанию HTTP/1.1:</span><span class="sxs-lookup"><span data-stu-id="f7077-397">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="f7077-398">В следующем примере *appsettings.json* для отдельной конечной точки устанавливается протокол подключения HTTP/1.1:</span><span class="sxs-lookup"><span data-stu-id="f7077-398">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="f7077-399">Указанные в коде протоколы переопределяют значения, заданные в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f7077-399">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="f7077-400">Конфигурация транспорта</span><span class="sxs-lookup"><span data-stu-id="f7077-400">Transport configuration</span></span>

<span data-ttu-id="f7077-401">Для проектов, где требуется использовать Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span><span class="sxs-lookup"><span data-stu-id="f7077-401">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="f7077-402">Добавляют зависимость для пакета [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) к файлу проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="f7077-402">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="f7077-403">Вызовите <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> для `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="f7077-403">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="f7077-404">Префиксы URL-адресов</span><span class="sxs-lookup"><span data-stu-id="f7077-404">URL prefixes</span></span>

<span data-ttu-id="f7077-405">Если вы используете `UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` или переменную среды `ASPNETCORE_URLS`, префиксы URL-адресов могут иметь любой из указанных ниже форматов.</span><span class="sxs-lookup"><span data-stu-id="f7077-405">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="f7077-406">Допустимы только префиксы URL-адресов HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7077-406">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="f7077-407">Kestrel не поддерживает HTTP при настройке привязок URL-адресов с помощью `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-407">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="f7077-408">IPv4-адрес с номером порта</span><span class="sxs-lookup"><span data-stu-id="f7077-408">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="f7077-409">`0.0.0.0` является особым случаем, соответствующим привязке ко всем IPv4-адресам.</span><span class="sxs-lookup"><span data-stu-id="f7077-409">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="f7077-410">IPv6-адрес с номером порта</span><span class="sxs-lookup"><span data-stu-id="f7077-410">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="f7077-411">`[::]` является IPv6-аналогом IPv4-адреса `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="f7077-411">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="f7077-412">Имя узла с номером порта</span><span class="sxs-lookup"><span data-stu-id="f7077-412">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="f7077-413">Имена узлов, `*` и `+`, не являются особыми.</span><span class="sxs-lookup"><span data-stu-id="f7077-413">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="f7077-414">Все, что не распознается как допустимый IP-адрес или `localhost`, привязывается ко всем IP-адресам IPv4 и IPv6.</span><span class="sxs-lookup"><span data-stu-id="f7077-414">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="f7077-415">Чтобы привязать разные имена узлов к разным приложениям ASP.NET Core по одному порту, используйте [HTTP.sys](xref:fundamentals/servers/httpsys) или обратный прокси-сервер, такой как IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="f7077-415">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="f7077-416">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="f7077-416">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="f7077-417">Имя узла `localhost` с номером порта или IP-адрес замыкания на себя с номером порта</span><span class="sxs-lookup"><span data-stu-id="f7077-417">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="f7077-418">Когда указан `localhost`, Kestrel пытается привязаться к обоим интерфейсам замыкания на себя IPv4 и IPv6.</span><span class="sxs-lookup"><span data-stu-id="f7077-418">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="f7077-419">Если запрошенный порт уже используется другой службой в одном из интерфейсов замыкания на себя, Kestrel не запускается.</span><span class="sxs-lookup"><span data-stu-id="f7077-419">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="f7077-420">Если один из интерфейсов замыкания на себя недоступен по любой другой причине (чаще всего, это отсутствие поддержки IPv6), Kestrel заносит в журнал предупреждение.</span><span class="sxs-lookup"><span data-stu-id="f7077-420">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="f7077-421">Фильтрация узлов</span><span class="sxs-lookup"><span data-stu-id="f7077-421">Host filtering</span></span>

<span data-ttu-id="f7077-422">Хотя Kestrel поддерживает конфигурации с использованием префиксов, такие как `http://example.com:5000`, Kestrel не учитывает имя узла.</span><span class="sxs-lookup"><span data-stu-id="f7077-422">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="f7077-423">Узел `localhost` является особым случаем, используемым для привязки к адресам замыкания на себя.</span><span class="sxs-lookup"><span data-stu-id="f7077-423">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="f7077-424">Любой узел, отличный от явного IP-адреса, привязывается ко всем общедоступным IP-адресам.</span><span class="sxs-lookup"><span data-stu-id="f7077-424">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="f7077-425">Заголовки `Host` не проверяются.</span><span class="sxs-lookup"><span data-stu-id="f7077-425">`Host` headers aren't validated.</span></span>

<span data-ttu-id="f7077-426">В качестве обходного решения используйте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="f7077-426">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="f7077-427">ПО промежуточного слоя фильтрации узлов предоставляется пакетом [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), который неявно предоставляется для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7077-427">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="f7077-428">ПО промежуточного слоя добавляется в <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, который вызывает <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="f7077-428">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="f7077-429">ПО промежуточного слоя фильтрации узлов отключено по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-429">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="f7077-430">Чтобы включить ПО промежуточного слоя, определите ключ `AllowedHosts` в *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="f7077-430">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="f7077-431">Значение представляет собой разделенный точками с запятой список имен узлов без номеров портов:</span><span class="sxs-lookup"><span data-stu-id="f7077-431">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="f7077-432">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f7077-432">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="f7077-433">[ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer) имеет также параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="f7077-433">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="f7077-434">ПО промежуточного слоя перенаправления заголовков и ПО промежуточного слоя фильтрации узлов обладают сходными возможностями для различных сценариев.</span><span class="sxs-lookup"><span data-stu-id="f7077-434">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="f7077-435">Параметр `AllowedHosts` с ПО промежуточного слоя перенаправления заголовков подходит для случаев, когда заголовок `Host` не сохраняется при переадресации запросов с помощью обратного прокси-сервера или подсистемы балансировки нагрузки.</span><span class="sxs-lookup"><span data-stu-id="f7077-435">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="f7077-436">Параметр `AllowedHosts` с ПО промежуточного слоя фильтрации узлов подходит для случаев, когда Kestrel используется в качестве общедоступного пограничного сервера или если заголовок `Host` пересылается напрямую.</span><span class="sxs-lookup"><span data-stu-id="f7077-436">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="f7077-437">Дополнительные сведения о ПО промежуточного слоя перенаправления заголовков см. в статье <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="f7077-437">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f7077-438">Kestrel — это кроссплатформенный [веб-сервер для ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="f7077-438">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="f7077-439">Веб-сервер Kestrel по умолчанию включается в шаблоны проектов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7077-439">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="f7077-440">Kestrel поддерживается в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="f7077-440">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="f7077-441">HTTPS</span><span class="sxs-lookup"><span data-stu-id="f7077-441">HTTPS</span></span>
* <span data-ttu-id="f7077-442">Непрозрачное обновление для поддержки [WebSocket](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="f7077-442">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="f7077-443">Сокеты UNIX для повышения производительности при работе за Nginx</span><span class="sxs-lookup"><span data-stu-id="f7077-443">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="f7077-444">HTTP/2 (за исключением macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="f7077-444">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="f7077-445">&dagger; HTTP/2 будет поддерживаться для macOS в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="f7077-445">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="f7077-446">Kestrel поддерживается на всех платформах и во всех версиях, поддерживаемых .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7077-446">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="f7077-447">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7077-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="f7077-448">Поддержка HTTP/2</span><span class="sxs-lookup"><span data-stu-id="f7077-448">HTTP/2 support</span></span>

<span data-ttu-id="f7077-449">Протокол [HTTP/2](https://httpwg.org/specs/rfc7540.html) доступен для приложений ASP.NET Core, если выполнены следующие базовые требования:</span><span class="sxs-lookup"><span data-stu-id="f7077-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="f7077-450">Операционная система&dagger;:</span><span class="sxs-lookup"><span data-stu-id="f7077-450">Operating system&dagger;</span></span>
  * <span data-ttu-id="f7077-451">Windows Server 2016 / Windows 10 или более поздних версий&Dagger;</span><span class="sxs-lookup"><span data-stu-id="f7077-451">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="f7077-452">Linux с OpenSSL 1.0.2 или более поздней версии (например, Ubuntu 16.04 или более поздней версии).</span><span class="sxs-lookup"><span data-stu-id="f7077-452">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="f7077-453">Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней</span><span class="sxs-lookup"><span data-stu-id="f7077-453">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="f7077-454">Подключение с поддержкой [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3).</span><span class="sxs-lookup"><span data-stu-id="f7077-454">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="f7077-455">Подключение TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="f7077-455">TLS 1.2 or later connection</span></span>

<span data-ttu-id="f7077-456">&dagger; HTTP/2 будет поддерживаться для macOS в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="f7077-456">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="f7077-457">&Dagger;Для Kestrel предусмотрена ограниченная поддержка HTTP/2 в Windows Server 2012 R2 и Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="f7077-457">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="f7077-458">Поддержка ограничена из-за небольшого числа поддерживаемых комплектов шифров TLS, доступных для этих операционных систем.</span><span class="sxs-lookup"><span data-stu-id="f7077-458">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="f7077-459">Для обеспечения безопасности TLS-подключений может потребоваться сертификат, созданный с использованием алгоритма ECDSA.</span><span class="sxs-lookup"><span data-stu-id="f7077-459">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="f7077-460">Если установлено подключение HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="f7077-460">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="f7077-461">По умолчанию протокол HTTP/2 отключен.</span><span class="sxs-lookup"><span data-stu-id="f7077-461">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="f7077-462">Дополнительные сведения о конфигурации см. в разделах [о параметрах Kestrel](#kestrel-options) и [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="f7077-462">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="f7077-463">Условия использования Kestrel с обратным прокси-сервером</span><span class="sxs-lookup"><span data-stu-id="f7077-463">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="f7077-464">Kestrel можно использовать отдельно или с *обратным прокси-сервером*, таким как [IIS](https://www.iis.net/), [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f7077-464">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="f7077-465">Обратный прокси-сервер получает HTTP-запросы из сети и пересылает их в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-465">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="f7077-466">Kestrel используется в качестве веб-сервера перехода (с выходом в Интернет).</span><span class="sxs-lookup"><span data-stu-id="f7077-466">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel взаимодействует с Интернетом напрямую, без обратного прокси-сервера](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="f7077-468">Kestrel используется в конфигурации обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="f7077-468">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel взаимодействует с Интернетом косвенно, через обратный прокси-сервер, такой как IIS, Nginx или Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="f7077-470">Любая из этих конфигураций — с обратным прокси-сервером и без него — является поддерживаемой конфигурацией для размещения.</span><span class="sxs-lookup"><span data-stu-id="f7077-470">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="f7077-471">Kestrel, используемый в качестве пограничного сервера без обратного прокси-сервера, не поддерживает общий доступ нескольких процессов к одним и тем же IP-адресам и портам.</span><span class="sxs-lookup"><span data-stu-id="f7077-471">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="f7077-472">Когда Kestrel настроен на ожидание передачи данных от порта, Kestrel обрабатывает весь трафик для этого порта независимо от заголовка `Host` запросов.</span><span class="sxs-lookup"><span data-stu-id="f7077-472">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="f7077-473">Поэтому обратный прокси-сервер, который может совместно использовать порты, способен пересылать запросы в Kestrel с уникальным IP-адресом и портом.</span><span class="sxs-lookup"><span data-stu-id="f7077-473">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="f7077-474">Даже если обратный прокси-сервер не требуется, его использование может оказаться удобным.</span><span class="sxs-lookup"><span data-stu-id="f7077-474">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="f7077-475">Обратный прокси-сервер:</span><span class="sxs-lookup"><span data-stu-id="f7077-475">A reverse proxy:</span></span>

* <span data-ttu-id="f7077-476">Может ограничить общедоступную контактную зону размещенных на нем приложений.</span><span class="sxs-lookup"><span data-stu-id="f7077-476">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="f7077-477">Предоставляет дополнительный уровень конфигурации и защиты.</span><span class="sxs-lookup"><span data-stu-id="f7077-477">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="f7077-478">Может лучше интегрироваться с существующей инфраструктурой.</span><span class="sxs-lookup"><span data-stu-id="f7077-478">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="f7077-479">Упрощает настройку балансировки нагрузки и безопасных подключений (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f7077-479">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="f7077-480">Сертификат X.509 требуется только обратному прокси-серверу, а сам этот сервер может обмениваться данными с серверами приложения во внутренней сети по обычному протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7077-480">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="f7077-481">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="f7077-481">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="f7077-482">Условия использования Kestrel в приложениях ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7077-482">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="f7077-483">Пакет [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f7077-483">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f7077-484">Шаблоны проектов ASP.NET Core используют Kestrel по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-484">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="f7077-485">В файле *Program.cs* код шаблона вызывает метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, который в фоновом режиме вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>.</span><span class="sxs-lookup"><span data-stu-id="f7077-485">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="f7077-486">Дополнительные сведения о `CreateDefaultBuilder` и построении узла, см. в разделе *Настройка узла*, разделы <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="f7077-486">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="f7077-487">Чтобы задать дополнительную конфигурацию после вызова `CreateDefaultBuilder`, используйте `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="f7077-487">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="f7077-488">Если приложение не вызывает `CreateDefaultBuilder`, чтобы настроить узел, вызовите <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **перед** вызовом `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="f7077-488">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="f7077-489">Параметры Kestrel</span><span class="sxs-lookup"><span data-stu-id="f7077-489">Kestrel options</span></span>

<span data-ttu-id="f7077-490">Веб-сервер Kestrel имеет ограничительные параметры конфигурации, которые удобно использовать в развертываниях с выходом в Интернет.</span><span class="sxs-lookup"><span data-stu-id="f7077-490">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="f7077-491">Задать ограничения для свойства <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> в классе <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="f7077-491">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="f7077-492">Свойство `Limits` содержит экземпляр класса <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="f7077-492">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="f7077-493">В следующих примерах используется пространство имен <xref:Microsoft.AspNetCore.Server.Kestrel.Core>.</span><span class="sxs-lookup"><span data-stu-id="f7077-493">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="f7077-494">Параметры Kestrel, которые настраиваются в C# коде в следующих примерах, можно также задать с помощью [поставщика конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="f7077-494">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="f7077-495">Например, поставщик конфигурации файла может загрузить конфигурацию Kestrel из файла *appsettings.JSON* или *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="f7077-495">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="f7077-496">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="f7077-496">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="f7077-497">Настройте Kestrel в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f7077-497">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="f7077-498">Внедрение экземпляра `IConfiguration` в класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="f7077-498">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="f7077-499">В следующем примере предполагается, что введенная конфигурация назначается свойству `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="f7077-499">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="f7077-500">В `Startup.ConfigureServices` загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel:</span><span class="sxs-lookup"><span data-stu-id="f7077-500">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="f7077-501">Настройте Kestrel при сборке узла:</span><span class="sxs-lookup"><span data-stu-id="f7077-501">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="f7077-502">В *Program.cs* загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-502">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="f7077-503">Оба предыдущих подхода работают с любым [поставщиком конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="f7077-503">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="f7077-504">Время ожидания проверки на активность</span><span class="sxs-lookup"><span data-stu-id="f7077-504">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="f7077-505">Получает или задает [время ожидания проверки на активность](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="f7077-505">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="f7077-506">Значение по умолчанию — 2 минуты.</span><span class="sxs-lookup"><span data-stu-id="f7077-506">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="f7077-507">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="f7077-507">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="f7077-508">Максимальное число одновременно открытых подключений TCP для всего приложения можно задать с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="f7077-508">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="f7077-509">Существует отдельный предел по подключениям, измененным с HTTP или HTTPS на другой протокол (например, по запросу WebSocket).</span><span class="sxs-lookup"><span data-stu-id="f7077-509">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="f7077-510">После изменения подключение не учитывается в пределе `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="f7077-510">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="f7077-511">По умолчанию максимальное число подключений не ограничено (null).</span><span class="sxs-lookup"><span data-stu-id="f7077-511">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="f7077-512">максимальный размер текста запроса;</span><span class="sxs-lookup"><span data-stu-id="f7077-512">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="f7077-513">По умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="f7077-513">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="f7077-514">Чтобы переопределить это ограничение в приложении MVC ASP.NET Core, мы рекомендуем использовать атрибут <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> в методе действия:</span><span class="sxs-lookup"><span data-stu-id="f7077-514">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="f7077-515">Приведенный ниже пример показывает, как настроить ограничение для приложения и каждого запроса:</span><span class="sxs-lookup"><span data-stu-id="f7077-515">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="f7077-516">Переопределите параметр для конкретного запроса в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="f7077-516">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="f7077-517">Если приложение пытается настроить ограничение для запроса после того, как оно начало считывать запрос, возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="f7077-517">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="f7077-518">Доступно свойство `IsReadOnly`, указывающее, что свойство `MaxRequestBodySize` находится в состоянии только для чтения и настраивать ограничение слишком поздно.</span><span class="sxs-lookup"><span data-stu-id="f7077-518">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="f7077-519">При запуске приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), который обслуживает [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module), ограничение на размер текста запроса Kestrel будет отключено, так как оно уже задается IIS.</span><span class="sxs-lookup"><span data-stu-id="f7077-519">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="f7077-520">минимальная скорость передачи данных в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="f7077-520">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="f7077-521">Kestrel каждую секунду проверяет, поступают ли данные с указанной скоростью в байтах в секунду.</span><span class="sxs-lookup"><span data-stu-id="f7077-521">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="f7077-522">Если скорость падает ниже минимума, для соединения истекает время ожидания. Льготный период — это количество времени, которое Kestrel дает клиенту на увеличение его скорости отправки до минимального уровня; в течение этого периода скорость не проверяется.</span><span class="sxs-lookup"><span data-stu-id="f7077-522">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="f7077-523">Льготный период помогает избежать разрыва соединений, которые первоначально отправляют данные с небольшой скоростью из-за медленного запуска TCP.</span><span class="sxs-lookup"><span data-stu-id="f7077-523">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="f7077-524">Минимальная скорость по умолчанию составляет 240 байт/с, льготный период равен 5 секундам.</span><span class="sxs-lookup"><span data-stu-id="f7077-524">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="f7077-525">Минимальная скорость также применяется к отклику.</span><span class="sxs-lookup"><span data-stu-id="f7077-525">A minimum rate also applies to the response.</span></span> <span data-ttu-id="f7077-526">Код для задания лимита запросов и лимита откликов различается только наличием `RequestBody` или `Response` в именах свойств и интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="f7077-526">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="f7077-527">Ниже приведен пример, показывающий, как настроить минимальную скорость передачи данных в *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f7077-527">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="f7077-528">Переопределите ограничения минимальной скорости для каждого запроса в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="f7077-528">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="f7077-529">В `HttpContext.Features` для запросов HTTP/2 отсутствуют возможности настройки скорости, указанные в предыдущем примере, так как изменение ограничений скорости для каждого запроса не поддерживается для HTTP/2 из-за поддержки протокола мультиплексирования запроса.</span><span class="sxs-lookup"><span data-stu-id="f7077-529">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="f7077-530">Ограничения скорости на уровне сервера, которые настроены с помощью `KestrelServerOptions.Limits`, по-прежнему применяются к подключениям HTTP/1.x и HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7077-530">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="f7077-531">Время ожидания для заголовков запросов</span><span class="sxs-lookup"><span data-stu-id="f7077-531">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="f7077-532">Получает или задает максимальное время, которое сервер уделяет получению заголовков запросов.</span><span class="sxs-lookup"><span data-stu-id="f7077-532">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="f7077-533">Значение по умолчанию — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="f7077-533">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="f7077-534">Максимальное число потоков на подключение</span><span class="sxs-lookup"><span data-stu-id="f7077-534">Maximum streams per connection</span></span>

<span data-ttu-id="f7077-535">`Http2.MaxStreamsPerConnection` ограничивает количество параллельных потоков запросов для одного соединения HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7077-535">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="f7077-536">Потоки сверх этого числа будут отклонены.</span><span class="sxs-lookup"><span data-stu-id="f7077-536">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="f7077-537">Значение по умолчанию — 100.</span><span class="sxs-lookup"><span data-stu-id="f7077-537">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="f7077-538">Размер таблицы заголовка</span><span class="sxs-lookup"><span data-stu-id="f7077-538">Header table size</span></span>

<span data-ttu-id="f7077-539">Декодер HPACK распаковывает заголовки HTTP для подключений HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7077-539">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="f7077-540">`Http2.HeaderTableSize` ограничивает размер таблицы сжатия заголовка, которую использует декодер HPACK.</span><span class="sxs-lookup"><span data-stu-id="f7077-540">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="f7077-541">Это значение указывается в октетах и должно быть больше нуля (0).</span><span class="sxs-lookup"><span data-stu-id="f7077-541">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="f7077-542">Значение по умолчанию — 4096.</span><span class="sxs-lookup"><span data-stu-id="f7077-542">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="f7077-543">Максимальный размер кадра</span><span class="sxs-lookup"><span data-stu-id="f7077-543">Maximum frame size</span></span>

<span data-ttu-id="f7077-544">`Http2.MaxFrameSize` указывает максимальный размер полезных данных в получаемом кадре подключения HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7077-544">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="f7077-545">Это значение указывается в октетах и должно находиться в пределах от 2^14 (16 384) до 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="f7077-545">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="f7077-546">Значение по умолчанию — 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="f7077-546">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="f7077-547">Максимальный размер запроса заголовка</span><span class="sxs-lookup"><span data-stu-id="f7077-547">Maximum request header size</span></span>

<span data-ttu-id="f7077-548">`Http2.MaxRequestHeaderFieldSize` указывает максимально допустимый размер значений заголовка запроса (в октетах).</span><span class="sxs-lookup"><span data-stu-id="f7077-548">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="f7077-549">Это ограничение применяется к имени и значению в их сжатых и несжатых представлениях.</span><span class="sxs-lookup"><span data-stu-id="f7077-549">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="f7077-550">Значение должно быть больше нуля.</span><span class="sxs-lookup"><span data-stu-id="f7077-550">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="f7077-551">Значение по умолчанию — 8 192.</span><span class="sxs-lookup"><span data-stu-id="f7077-551">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="f7077-552">Размер окна начального подключения</span><span class="sxs-lookup"><span data-stu-id="f7077-552">Initial connection window size</span></span>

<span data-ttu-id="f7077-553">`Http2.InitialConnectionWindowSize` указывает максимальное значение данных тела запроса (в байтах), буферизируемое сервером за один раз, для всех запросов (потоков) на каждое соединение.</span><span class="sxs-lookup"><span data-stu-id="f7077-553">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="f7077-554">Размеры запросов также ограничиваются параметром `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="f7077-554">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="f7077-555">Значение должно быть больше или равно 65 535 и меньше 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="f7077-555">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="f7077-556">Значение по умолчанию — 128 КБ (131 072).</span><span class="sxs-lookup"><span data-stu-id="f7077-556">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="f7077-557">Размер окна начального потока</span><span class="sxs-lookup"><span data-stu-id="f7077-557">Initial stream window size</span></span>

<span data-ttu-id="f7077-558">`Http2.InitialStreamWindowSize` указывает максимальное значение данных тела запроса (в байтах), буферизируемое сервером за один раз, для каждого запроса (потока).</span><span class="sxs-lookup"><span data-stu-id="f7077-558">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="f7077-559">Размеры запросов также ограничиваются параметром `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="f7077-559">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="f7077-560">Значение должно быть больше или равно 65 535 и меньше 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="f7077-560">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="f7077-561">Значение по умолчанию — 96 КБ (98 304).</span><span class="sxs-lookup"><span data-stu-id="f7077-561">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="f7077-562">Синхронный ввод-вывод</span><span class="sxs-lookup"><span data-stu-id="f7077-562">Synchronous I/O</span></span>

<span data-ttu-id="f7077-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> определяет, разрешены ли синхронные операции ввода-вывода для запроса и ответа.</span><span class="sxs-lookup"><span data-stu-id="f7077-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="f7077-564">Значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="f7077-564">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f7077-565">Выполнение большого числа заблокированных операций синхронного ввода-вывода может привести к перегрузке пула потоков и зависанию приложения.</span><span class="sxs-lookup"><span data-stu-id="f7077-565">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="f7077-566">Включайте `AllowSynchronousIO`, только если вы используете библиотеку, которая не поддерживает асинхронные операции ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="f7077-566">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="f7077-567">В примере ниже включаются синхронные операции ввода-вывода:</span><span class="sxs-lookup"><span data-stu-id="f7077-567">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="f7077-568">Сведения о других параметрах и ограничениях Kestrel см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="f7077-568">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="f7077-569">Конфигурация конечной точки</span><span class="sxs-lookup"><span data-stu-id="f7077-569">Endpoint configuration</span></span>

<span data-ttu-id="f7077-570">По умолчанию платформа ASP.NET Core привязана к:</span><span class="sxs-lookup"><span data-stu-id="f7077-570">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="f7077-571">`https://localhost:5001` (если присутствует локальный сертификат разработки)</span><span class="sxs-lookup"><span data-stu-id="f7077-571">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="f7077-572">Укажите URL-адреса с помощью следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="f7077-572">Specify URLs using the:</span></span>

* <span data-ttu-id="f7077-573">Переменная среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="f7077-573">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="f7077-574">Аргументы командной строки `--urls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-574">`--urls` command-line argument.</span></span>
* <span data-ttu-id="f7077-575">Ключ конфигурации узла `urls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-575">`urls` host configuration key.</span></span>
* <span data-ttu-id="f7077-576">Метод расширения `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-576">`UseUrls` extension method.</span></span>

<span data-ttu-id="f7077-577">Значение, указанное с помощью этих подходов, может быть одной или несколькими конечными точками HTTP и HTTPS (HTTPS при наличии сертификата по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="f7077-577">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="f7077-578">Настройте значение в виде списка с разделением точкой с запятой (например, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="f7077-578">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="f7077-579">Дополнительные сведения о таких подходах см. в разделах [URL-адреса сервера](xref:fundamentals/host/web-host#server-urls) и [Переопределение конфигурации](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="f7077-579">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="f7077-580">Сертификат разработки создается, когда:</span><span class="sxs-lookup"><span data-stu-id="f7077-580">A development certificate is created:</span></span>

* <span data-ttu-id="f7077-581">установлен [пакет SDK для .NET Core](/dotnet/core/sdk);</span><span class="sxs-lookup"><span data-stu-id="f7077-581">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="f7077-582">используется [средство dev-certs](xref:aspnetcore-2.1#https) для создания сертификата.</span><span class="sxs-lookup"><span data-stu-id="f7077-582">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="f7077-583">В некоторых браузерах требуется явное разрешение доверять локальному сертификату разработки.</span><span class="sxs-lookup"><span data-stu-id="f7077-583">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="f7077-584">Шаблоны проектов настраивают приложения так, чтобы они запускались на базе HTTPS по умолчанию и включали [поддержку перенаправления HTTPS и HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="f7077-584">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="f7077-585">Вызовите методы <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> или <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> из <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>, чтобы настроить префиксы URL-адресов и порты для Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-585">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="f7077-586">`UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` и переменная среды `ASPNETCORE_URLS` тоже работают, однако на них распространяются ограничения, указанные далее в этой статье (для конфигурации конечной точки HTTPS требуется сертификат по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="f7077-586">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="f7077-587">Конфигурация `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="f7077-587">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="f7077-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="f7077-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="f7077-589">Указывает конфигурацию `Action` для выполнения каждой заданной конечной точки.</span><span class="sxs-lookup"><span data-stu-id="f7077-589">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="f7077-590">Если вызвать `ConfigureEndpointDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.</span><span class="sxs-lookup"><span data-stu-id="f7077-590">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="f7077-591">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-591">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="f7077-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="f7077-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="f7077-593">Указывает конфигурацию `Action` для выполнения каждой конечной точки HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f7077-593">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="f7077-594">Если вызвать `ConfigureHttpsDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.</span><span class="sxs-lookup"><span data-stu-id="f7077-594">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="f7077-595">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-595">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="f7077-596">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="f7077-596">Configure(IConfiguration)</span></span>

<span data-ttu-id="f7077-597">Создает загрузчик конфигурации для настройки Kestrel, который принимает <xref:Microsoft.Extensions.Configuration.IConfiguration> в качестве входных данных.</span><span class="sxs-lookup"><span data-stu-id="f7077-597">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="f7077-598">Для Kestrel конфигурация должна быть ограничена разделом конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f7077-598">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="f7077-599">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="f7077-599">ListenOptions.UseHttps</span></span>

<span data-ttu-id="f7077-600">Настройте Kestrel для использования протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f7077-600">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="f7077-601">Расширения `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="f7077-601">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="f7077-602">`UseHttps`. Настройте Kestrel для использования протокола HTTPS с сертификатом по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-602">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="f7077-603">Создает исключение, если сертификат по умолчанию не настроен.</span><span class="sxs-lookup"><span data-stu-id="f7077-603">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="f7077-604">Параметры `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="f7077-604">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="f7077-605">`filename` — это путь и имя файла сертификата, связанного с каталогом, где находятся файлы содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="f7077-605">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="f7077-606">`password` — это пароль для доступа к данным сертификата X.509.</span><span class="sxs-lookup"><span data-stu-id="f7077-606">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="f7077-607">`configureOptions` — это `Action` для настройки `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="f7077-607">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="f7077-608">Возвращает `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="f7077-608">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="f7077-609">`storeName` — это хранилище сертификатов, из которого выполняется загрузка сертификата.</span><span class="sxs-lookup"><span data-stu-id="f7077-609">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="f7077-610">`subject` — это имя субъекта для сертификата.</span><span class="sxs-lookup"><span data-stu-id="f7077-610">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="f7077-611">`allowInvalid` указывает, следует ли учитывать недопустимые сертификаты, например самозаверяющие сертификаты.</span><span class="sxs-lookup"><span data-stu-id="f7077-611">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="f7077-612">`location` — это расположение хранилища, из которого загружается сертификат.</span><span class="sxs-lookup"><span data-stu-id="f7077-612">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="f7077-613">`serverCertificate` — это сертификат X.509.</span><span class="sxs-lookup"><span data-stu-id="f7077-613">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="f7077-614">В рабочей среде необходимо явно настроить HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f7077-614">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="f7077-615">Как минимум необходимо указать сертификат по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-615">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="f7077-616">Поддерживаемые конфигурации, описанные далее:</span><span class="sxs-lookup"><span data-stu-id="f7077-616">Supported configurations described next:</span></span>

* <span data-ttu-id="f7077-617">Отсутствие конфигурации</span><span class="sxs-lookup"><span data-stu-id="f7077-617">No configuration</span></span>
* <span data-ttu-id="f7077-618">Замена сертификата по умолчанию из конфигурации</span><span class="sxs-lookup"><span data-stu-id="f7077-618">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="f7077-619">Изменение значений по умолчанию в коде</span><span class="sxs-lookup"><span data-stu-id="f7077-619">Change the defaults in code</span></span>

<span data-ttu-id="f7077-620">*Отсутствие конфигурации*</span><span class="sxs-lookup"><span data-stu-id="f7077-620">*No configuration*</span></span>

<span data-ttu-id="f7077-621">Kestrel ожидает передачи данных через `http://localhost:5000` и `https://localhost:5001` (если доступен сертификат по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="f7077-621">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="f7077-622">*Замена сертификата по умолчанию из конфигурации*</span><span class="sxs-lookup"><span data-stu-id="f7077-622">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="f7077-623">`CreateDefaultBuilder` по умолчанию вызывает `Configure(context.Configuration.GetSection("Kestrel"))`, чтобы загрузить конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-623">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="f7077-624">Kestrel имеет доступ к схеме конфигурации параметров приложения HTTPS по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-624">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="f7077-625">Настройте несколько конечных точек, включая URL-адреса и сертификаты для использования, либо из файла на диске, либо из хранилища сертификатов.</span><span class="sxs-lookup"><span data-stu-id="f7077-625">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="f7077-626">В следующем примере *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f7077-626">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="f7077-627">Установите для **AllowInvalid** значение `true`, чтобы разрешить использование недопустимых сертификатов (например, самозаверяющих сертификатов).</span><span class="sxs-lookup"><span data-stu-id="f7077-627">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="f7077-628">Любая конечная точка HTTPS, которая не указывает сертификат (**HttpsDefaultCert** в следующем примере), будет использовать сертификат, определенный в разделе **Certificates** (Сертификаты) > **Default** (По умолчанию), или сертификат разработки.</span><span class="sxs-lookup"><span data-stu-id="f7077-628">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="f7077-629">Вместо использования параметров **Path** и **Password** для узла сертификата можно указать сертификат с помощью полей хранилища сертификатов.</span><span class="sxs-lookup"><span data-stu-id="f7077-629">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="f7077-630">Например, сертификат из раздела **Сертификаты** > **По умолчанию** можно указать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f7077-630">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="f7077-631">Примечания к схеме.</span><span class="sxs-lookup"><span data-stu-id="f7077-631">Schema notes:</span></span>

* <span data-ttu-id="f7077-632">Регистр букв в именах конечных точек не учитывается.</span><span class="sxs-lookup"><span data-stu-id="f7077-632">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="f7077-633">Например, `HTTPS` и `Https` являются допустимыми.</span><span class="sxs-lookup"><span data-stu-id="f7077-633">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="f7077-634">Параметр `Url` является обязательным для каждой конечной точки.</span><span class="sxs-lookup"><span data-stu-id="f7077-634">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="f7077-635">Формат этого параметра такой же, как для параметра конфигурации `Urls` верхнего уровня, только он ограничен одиночным значением.</span><span class="sxs-lookup"><span data-stu-id="f7077-635">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="f7077-636">Эти конечные точки заменяют конечные точки, определенные в конфигурации `Urls` верхнего уровня, а не дополняют их.</span><span class="sxs-lookup"><span data-stu-id="f7077-636">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="f7077-637">Конечные точки, определенные в коде через `Listen`, объединяются с конечными точками, определенными в разделе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f7077-637">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="f7077-638">Раздел `Certificate` является необязательным.</span><span class="sxs-lookup"><span data-stu-id="f7077-638">The `Certificate` section is optional.</span></span> <span data-ttu-id="f7077-639">Если раздел `Certificate` не указан, используются значения по умолчанию, определенные в предыдущих сценариях.</span><span class="sxs-lookup"><span data-stu-id="f7077-639">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="f7077-640">Если значений по умолчанию нет, сервер выдает исключение и не запускается.</span><span class="sxs-lookup"><span data-stu-id="f7077-640">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="f7077-641">Раздел `Certificate` поддерживает сертификаты **Path**&ndash;**Password** и **Subject**&ndash;**Store**.</span><span class="sxs-lookup"><span data-stu-id="f7077-641">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="f7077-642">Таким образом, можно определить любое количество конечных точек, если это не приводит к конфликту портов.</span><span class="sxs-lookup"><span data-stu-id="f7077-642">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="f7077-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` возвращает `KestrelConfigurationLoader` с методом `.Endpoint(string name, listenOptions => { })`, который может использоваться в качестве дополнения для параметров настроенной конечной точки:</span><span class="sxs-lookup"><span data-stu-id="f7077-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="f7077-644">Можно обратиться напрямую к `KestrelServerOptions.ConfigurationLoader`, чтобы и далее выполнять итерацию с существующим загрузчиком, например, предоставленным <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="f7077-644">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="f7077-645">Раздел конфигурации для каждой конечной точки доступен в параметрах в методе `Endpoint`, чтобы можно было прочитать пользовательские параметры.</span><span class="sxs-lookup"><span data-stu-id="f7077-645">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="f7077-646">Можно загрузить несколько конфигураций, снова вызвав `options.Configure(context.Configuration.GetSection("{SECTION}"))` с другим разделом.</span><span class="sxs-lookup"><span data-stu-id="f7077-646">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="f7077-647">Используется только последняя конфигурация, если явным образом не вызвать `Load` в предыдущих экземплярах.</span><span class="sxs-lookup"><span data-stu-id="f7077-647">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="f7077-648">Метапакет не вызывает `Load`, чтобы можно было заменить его раздел конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-648">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="f7077-649">`KestrelConfigurationLoader` отражает семейство API `Listen` из `KestrelServerOptions` как перегрузки `Endpoint`, чтобы можно было настроить конечные точки кода и конфигурации в одном месте.</span><span class="sxs-lookup"><span data-stu-id="f7077-649">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="f7077-650">Эти перегрузки не используют имена и используют только параметры по умолчанию из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f7077-650">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="f7077-651">*Изменение значений по умолчанию в коде*</span><span class="sxs-lookup"><span data-stu-id="f7077-651">*Change the defaults in code*</span></span>

<span data-ttu-id="f7077-652">Можно использовать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults` для изменения параметров по умолчанию для `ListenOptions` и `HttpsConnectionAdapterOptions`, включая переопределение сертификата по умолчанию, указанного в предыдущем сценарии.</span><span class="sxs-lookup"><span data-stu-id="f7077-652">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="f7077-653">Необходимо вызвать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults`, прежде чем настраивать конечные точки.</span><span class="sxs-lookup"><span data-stu-id="f7077-653">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="f7077-654">*Поддержка Kestrel для SNI*</span><span class="sxs-lookup"><span data-stu-id="f7077-654">*Kestrel support for SNI*</span></span>

<span data-ttu-id="f7077-655">Можно использовать [указание имени сервера (SNI)](https://tools.ietf.org/html/rfc6066#section-3) для размещения нескольких доменов в одном IP-адресе и порте.</span><span class="sxs-lookup"><span data-stu-id="f7077-655">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="f7077-656">Для использования SNI клиент отправляет имя узла для безопасного сеанса серверу во время подтверждения TLS, чтобы сервер предоставил правильный сертификат.</span><span class="sxs-lookup"><span data-stu-id="f7077-656">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="f7077-657">Клиент использует предоставленный сертификат для зашифрованного соединения с сервером во время безопасного сеанса, который следует после подтверждения TLS.</span><span class="sxs-lookup"><span data-stu-id="f7077-657">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="f7077-658">Kestrel поддерживает SNI через обратный вызов `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="f7077-658">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="f7077-659">Функция обратного вызова используется один раз за подключение, чтобы приложение проверило имя узла и выбрало соответствующий сертификат.</span><span class="sxs-lookup"><span data-stu-id="f7077-659">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="f7077-660">Поддержка SNI требует:</span><span class="sxs-lookup"><span data-stu-id="f7077-660">SNI support requires:</span></span>

* <span data-ttu-id="f7077-661">Запуск на целевой платформе `netcoreapp2.1` или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="f7077-661">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="f7077-662">В `net461` или более поздней версии обратный вызов выполняется, но `name` всегда имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="f7077-662">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="f7077-663">`name` также имеет значение `null`, если клиент не предоставляет параметр имени узла при подтверждении TLS.</span><span class="sxs-lookup"><span data-stu-id="f7077-663">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="f7077-664">Все веб-сайты выполняются на одном и том же экземпляре Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-664">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="f7077-665">Kestrel не поддерживает совместное использование IP-адреса и порта на нескольких экземплярах без обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="f7077-665">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="f7077-666">Ведение журнала подключения</span><span class="sxs-lookup"><span data-stu-id="f7077-666">Connection logging</span></span>

<span data-ttu-id="f7077-667">Вызовите <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>, чтобы выдать журналы уровня отладки для обмена данными на уровне байтов в рамках подключения.</span><span class="sxs-lookup"><span data-stu-id="f7077-667">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="f7077-668">Ведение журнала подключения полезно для устранения неполадок, связанных с низкоуровневым взаимодействием, например при TLS-шифровании и работе за прокси-серверами.</span><span class="sxs-lookup"><span data-stu-id="f7077-668">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="f7077-669">Если `UseConnectionLogging` поместить перед `UseHttps`, в журнале регистрируется зашифрованный трафик.</span><span class="sxs-lookup"><span data-stu-id="f7077-669">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="f7077-670">Если `UseConnectionLogging` поместить после `UseHttps`, в журнале регистрируется расшифрованный трафик.</span><span class="sxs-lookup"><span data-stu-id="f7077-670">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="f7077-671">Привязка к TCP-сокету</span><span class="sxs-lookup"><span data-stu-id="f7077-671">Bind to a TCP socket</span></span>

<span data-ttu-id="f7077-672">Метод <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> выполняет привязку к TCP-сокету, а лямбда-выражение параметров позволяет настроить конфигурацию сертификата X.509:</span><span class="sxs-lookup"><span data-stu-id="f7077-672">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="f7077-673">В этом примере настраивается HTTPS для конечной точки с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="f7077-673">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="f7077-674">С помощью этого API можно настроить и другие параметры Kestrel для отдельных конечных точек.</span><span class="sxs-lookup"><span data-stu-id="f7077-674">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="f7077-675">Привязка к сокету UNIX</span><span class="sxs-lookup"><span data-stu-id="f7077-675">Bind to a Unix socket</span></span>

<span data-ttu-id="f7077-676">Вы можете прослушивать сокет UNIX с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, чтобы улучшить производительность Nginx, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="f7077-676">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="f7077-677">В файле конфигурации Nginx установите для записи `server` > `location` > `proxy_pass` значение `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="f7077-677">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="f7077-678">`{KESTREL SOCKET}` — это имя сокета, предоставленного для <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (как `kestrel-test.sock` в предыдущем примере).</span><span class="sxs-lookup"><span data-stu-id="f7077-678">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="f7077-679">Убедитесь, что сокет доступен для записи Nginx (например, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="f7077-679">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="f7077-680">Порт 0</span><span class="sxs-lookup"><span data-stu-id="f7077-680">Port 0</span></span>

<span data-ttu-id="f7077-681">Если указать номер порта `0`, Kestrel динамически привязывается к доступному порту.</span><span class="sxs-lookup"><span data-stu-id="f7077-681">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="f7077-682">Следующий пример показывает, как определить, к какому порту фактически привязан Kestrel во время выполнения:</span><span class="sxs-lookup"><span data-stu-id="f7077-682">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="f7077-683">Когда приложение выполняется, в выходных данных в окне консоли указывается динамический порт, по которому можно связаться с приложением:</span><span class="sxs-lookup"><span data-stu-id="f7077-683">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="f7077-684">Ограничения</span><span class="sxs-lookup"><span data-stu-id="f7077-684">Limitations</span></span>

<span data-ttu-id="f7077-685">Настройте конечные точки с помощью следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="f7077-685">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="f7077-686">Аргументы командной строки `--urls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-686">`--urls` command-line argument</span></span>
* <span data-ttu-id="f7077-687">Ключ конфигурации узла `urls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-687">`urls` host configuration key</span></span>
* <span data-ttu-id="f7077-688">Переменная среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="f7077-688">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="f7077-689">Эти методы удобны, если нужно, чтобы код работал с серверами, отличными от Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-689">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="f7077-690">Не забывайте о следующих ограничениях.</span><span class="sxs-lookup"><span data-stu-id="f7077-690">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="f7077-691">С этими подходами нельзя использовать HTTPS, если в конфигурации конечной точки HTTPS не предоставлен сертификат по умолчанию (например, с помощью конфигурации `KestrelServerOptions` или файла конфигурации, как показано выше в этом разделе).</span><span class="sxs-lookup"><span data-stu-id="f7077-691">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="f7077-692">Если подходы `Listen` и `UseUrls` используются одновременно, конечные точки `Listen` переопределяют конечные точки `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-692">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="f7077-693">Конфигурация конечной точки IIS</span><span class="sxs-lookup"><span data-stu-id="f7077-693">IIS endpoint configuration</span></span>

<span data-ttu-id="f7077-694">При использовании служб IIS привязки URL-адресов для IIS переопределяют привязки, заданные `Listen` или `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-694">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="f7077-695">Дополнительные сведения см. в статье [Модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="f7077-695">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="f7077-696">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="f7077-696">ListenOptions.Protocols</span></span>

<span data-ttu-id="f7077-697">Свойство `Protocols` устанавливает протоколы HTTP (`HttpProtocols`), разрешенные для конечной точки подключения или для сервера.</span><span class="sxs-lookup"><span data-stu-id="f7077-697">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="f7077-698">Значение свойства `Protocols` должно входить в перечисление `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="f7077-698">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="f7077-699">Значение перечисления `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="f7077-699">`HttpProtocols` enum value</span></span> | <span data-ttu-id="f7077-700">Допустимый протокол подключения</span><span class="sxs-lookup"><span data-stu-id="f7077-700">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="f7077-701">Только HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="f7077-701">HTTP/1.1 only.</span></span> <span data-ttu-id="f7077-702">Можно использовать с протоколом TLS или без него.</span><span class="sxs-lookup"><span data-stu-id="f7077-702">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="f7077-703">Только HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7077-703">HTTP/2 only.</span></span> <span data-ttu-id="f7077-704">Может использоваться без TLS только в том случае, если клиент поддерживает [режим предварительного знания](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="f7077-704">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="f7077-705">HTTP/1.1 и HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7077-705">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="f7077-706">Для использования HTTP/2 требуется протокол TLS и подключение с [согласованием протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3); при их отсутствии используется подключение по умолчанию HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="f7077-706">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="f7077-707">По умолчанию используется протокол HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="f7077-707">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="f7077-708">Ограничения TLS для HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="f7077-708">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="f7077-709">TSL 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="f7077-709">TLS version 1.2 or later</span></span>
* <span data-ttu-id="f7077-710">Повторное согласование отключено.</span><span class="sxs-lookup"><span data-stu-id="f7077-710">Renegotiation disabled</span></span>
* <span data-ttu-id="f7077-711">Сжатие отключено.</span><span class="sxs-lookup"><span data-stu-id="f7077-711">Compression disabled</span></span>
* <span data-ttu-id="f7077-712">Минимальные размеры обмена временными ключами:</span><span class="sxs-lookup"><span data-stu-id="f7077-712">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="f7077-713">эллиптическая кривая Диффи—Хелмана (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: не менее 224 бит;</span><span class="sxs-lookup"><span data-stu-id="f7077-713">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="f7077-714">конечное поле Диффи—Хелмана (DHE) &lbrack;`TLS12`&rbrack;: не менее 2048 бит.</span><span class="sxs-lookup"><span data-stu-id="f7077-714">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="f7077-715">Набор шифров не внесен в список блокировок.</span><span class="sxs-lookup"><span data-stu-id="f7077-715">Cipher suite not blacklisted</span></span>

<span data-ttu-id="f7077-716">По умолчанию поддерживается `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; с эллиптической кривой P-256 &lbrack;`FIPS186`&rbrack;.</span><span class="sxs-lookup"><span data-stu-id="f7077-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="f7077-717">Следующий пример разрешает подключения HTTP/1.1 и HTTP/2 через порт 8000.</span><span class="sxs-lookup"><span data-stu-id="f7077-717">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="f7077-718">Эти подключения шифруются по протоколу TLS с использованием предоставленного сертификата:</span><span class="sxs-lookup"><span data-stu-id="f7077-718">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="f7077-719">Также вы можете создать реализацию `IConnectionAdapter` для фильтрации подтверждений TLS для каждого соединения по конкретным шифрам:</span><span class="sxs-lookup"><span data-stu-id="f7077-719">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="f7077-720">*Выбор протокола из конфигурации*</span><span class="sxs-lookup"><span data-stu-id="f7077-720">*Set the protocol from configuration*</span></span>

<span data-ttu-id="f7077-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> по умолчанию вызывает `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, чтобы загрузить конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="f7077-722">В следующем примере *appsettings.json* для всех конечных точек Kestrel устанавливается протокол подключения по умолчанию (HTTP/1.1 и HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="f7077-722">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="f7077-723">В следующем примере файла конфигурации задается протокол соединения для конкретной конечной точки:</span><span class="sxs-lookup"><span data-stu-id="f7077-723">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="f7077-724">Указанные в коде протоколы переопределяют значения, заданные в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f7077-724">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="f7077-725">Конфигурация транспорта</span><span class="sxs-lookup"><span data-stu-id="f7077-725">Transport configuration</span></span>

<span data-ttu-id="f7077-726">После выпуска ASP.NET Core 2.1 транспорт Kestrel по умолчанию основан не на Libuv, а на управляемых сокетах.</span><span class="sxs-lookup"><span data-stu-id="f7077-726">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="f7077-727">Это критическое изменение для приложений ASP.NET Core 2.0, которые обновляются до версии 2.1, если они вызывают <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> и зависят от одного из следующих пакетов:</span><span class="sxs-lookup"><span data-stu-id="f7077-727">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="f7077-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (прямая ссылка на пакет)</span><span class="sxs-lookup"><span data-stu-id="f7077-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="f7077-729">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="f7077-729">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="f7077-730">Для проектов, где требуется использовать Libuv:</span><span class="sxs-lookup"><span data-stu-id="f7077-730">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="f7077-731">Добавляют зависимость для пакета [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) к файлу проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="f7077-731">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="f7077-732">Вызов <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="f7077-732">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="f7077-733">Префиксы URL-адресов</span><span class="sxs-lookup"><span data-stu-id="f7077-733">URL prefixes</span></span>

<span data-ttu-id="f7077-734">Если вы используете `UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` или переменную среды `ASPNETCORE_URLS`, префиксы URL-адресов могут иметь любой из указанных ниже форматов.</span><span class="sxs-lookup"><span data-stu-id="f7077-734">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="f7077-735">Допустимы только префиксы URL-адресов HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7077-735">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="f7077-736">Kestrel не поддерживает HTTP при настройке привязок URL-адресов с помощью `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-736">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="f7077-737">IPv4-адрес с номером порта</span><span class="sxs-lookup"><span data-stu-id="f7077-737">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="f7077-738">`0.0.0.0` является особым случаем, соответствующим привязке ко всем IPv4-адресам.</span><span class="sxs-lookup"><span data-stu-id="f7077-738">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="f7077-739">IPv6-адрес с номером порта</span><span class="sxs-lookup"><span data-stu-id="f7077-739">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="f7077-740">`[::]` является IPv6-аналогом IPv4-адреса `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="f7077-740">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="f7077-741">Имя узла с номером порта</span><span class="sxs-lookup"><span data-stu-id="f7077-741">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="f7077-742">Имена узлов, `*` и `+`, не являются особыми.</span><span class="sxs-lookup"><span data-stu-id="f7077-742">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="f7077-743">Все, что не распознается как допустимый IP-адрес или `localhost`, привязывается ко всем IP-адресам IPv4 и IPv6.</span><span class="sxs-lookup"><span data-stu-id="f7077-743">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="f7077-744">Чтобы привязать разные имена узлов к разным приложениям ASP.NET Core по одному порту, используйте [HTTP.sys](xref:fundamentals/servers/httpsys) или обратный прокси-сервер, такой как IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="f7077-744">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="f7077-745">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="f7077-745">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="f7077-746">Имя узла `localhost` с номером порта или IP-адрес замыкания на себя с номером порта</span><span class="sxs-lookup"><span data-stu-id="f7077-746">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="f7077-747">Когда указан `localhost`, Kestrel пытается привязаться к обоим интерфейсам замыкания на себя IPv4 и IPv6.</span><span class="sxs-lookup"><span data-stu-id="f7077-747">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="f7077-748">Если запрошенный порт уже используется другой службой в одном из интерфейсов замыкания на себя, Kestrel не запускается.</span><span class="sxs-lookup"><span data-stu-id="f7077-748">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="f7077-749">Если один из интерфейсов замыкания на себя недоступен по любой другой причине (чаще всего, это отсутствие поддержки IPv6), Kestrel заносит в журнал предупреждение.</span><span class="sxs-lookup"><span data-stu-id="f7077-749">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="f7077-750">Фильтрация узлов</span><span class="sxs-lookup"><span data-stu-id="f7077-750">Host filtering</span></span>

<span data-ttu-id="f7077-751">Хотя Kestrel поддерживает конфигурации с использованием префиксов, такие как `http://example.com:5000`, Kestrel не учитывает имя узла.</span><span class="sxs-lookup"><span data-stu-id="f7077-751">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="f7077-752">Узел `localhost` является особым случаем, используемым для привязки к адресам замыкания на себя.</span><span class="sxs-lookup"><span data-stu-id="f7077-752">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="f7077-753">Любой узел, отличный от явного IP-адреса, привязывается ко всем общедоступным IP-адресам.</span><span class="sxs-lookup"><span data-stu-id="f7077-753">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="f7077-754">Заголовки `Host` не проверяются.</span><span class="sxs-lookup"><span data-stu-id="f7077-754">`Host` headers aren't validated.</span></span>

<span data-ttu-id="f7077-755">В качестве обходного решения используйте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="f7077-755">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="f7077-756">ПО промежуточного слоя фильтрации узла предоставляется пакетом [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), который входит в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 или 2.2).</span><span class="sxs-lookup"><span data-stu-id="f7077-756">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="f7077-757">ПО промежуточного слоя добавляется в <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, который вызывает <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="f7077-757">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="f7077-758">ПО промежуточного слоя фильтрации узлов отключено по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-758">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="f7077-759">Чтобы включить ПО промежуточного слоя, определите ключ `AllowedHosts` в *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="f7077-759">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="f7077-760">Значение представляет собой разделенный точками с запятой список имен узлов без номеров портов:</span><span class="sxs-lookup"><span data-stu-id="f7077-760">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="f7077-761">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f7077-761">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="f7077-762">[ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer) имеет также параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="f7077-762">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="f7077-763">ПО промежуточного слоя перенаправления заголовков и ПО промежуточного слоя фильтрации узлов обладают сходными возможностями для различных сценариев.</span><span class="sxs-lookup"><span data-stu-id="f7077-763">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="f7077-764">Параметр `AllowedHosts` с ПО промежуточного слоя перенаправления заголовков подходит для случаев, когда заголовок `Host` не сохраняется при переадресации запросов с помощью обратного прокси-сервера или подсистемы балансировки нагрузки.</span><span class="sxs-lookup"><span data-stu-id="f7077-764">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="f7077-765">Параметр `AllowedHosts` с ПО промежуточного слоя фильтрации узлов подходит для случаев, когда Kestrel используется в качестве общедоступного пограничного сервера или если заголовок `Host` пересылается напрямую.</span><span class="sxs-lookup"><span data-stu-id="f7077-765">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="f7077-766">Дополнительные сведения о ПО промежуточного слоя перенаправления заголовков см. в статье <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="f7077-766">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="f7077-767">Kestrel — это кроссплатформенный [веб-сервер для ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="f7077-767">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="f7077-768">Веб-сервер Kestrel по умолчанию включается в шаблоны проектов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7077-768">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="f7077-769">Kestrel поддерживается в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="f7077-769">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="f7077-770">HTTPS</span><span class="sxs-lookup"><span data-stu-id="f7077-770">HTTPS</span></span>
* <span data-ttu-id="f7077-771">Непрозрачное обновление для поддержки [WebSocket](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="f7077-771">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="f7077-772">Сокеты UNIX для повышения производительности при работе за Nginx</span><span class="sxs-lookup"><span data-stu-id="f7077-772">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="f7077-773">Kestrel поддерживается на всех платформах и во всех версиях, поддерживаемых .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7077-773">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="f7077-774">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7077-774">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="f7077-775">Условия использования Kestrel с обратным прокси-сервером</span><span class="sxs-lookup"><span data-stu-id="f7077-775">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="f7077-776">Kestrel можно использовать отдельно или с *обратным прокси-сервером*, таким как [IIS](https://www.iis.net/), [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f7077-776">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="f7077-777">Обратный прокси-сервер получает HTTP-запросы из сети и пересылает их в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-777">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="f7077-778">Kestrel используется в качестве веб-сервера перехода (с выходом в Интернет).</span><span class="sxs-lookup"><span data-stu-id="f7077-778">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel взаимодействует с Интернетом напрямую, без обратного прокси-сервера](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="f7077-780">Kestrel используется в конфигурации обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="f7077-780">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel взаимодействует с Интернетом косвенно, через обратный прокси-сервер, такой как IIS, Nginx или Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="f7077-782">Любая из этих конфигураций — с обратным прокси-сервером и без него — является поддерживаемой конфигурацией для размещения.</span><span class="sxs-lookup"><span data-stu-id="f7077-782">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="f7077-783">Kestrel, используемый в качестве пограничного сервера без обратного прокси-сервера, не поддерживает общий доступ нескольких процессов к одним и тем же IP-адресам и портам.</span><span class="sxs-lookup"><span data-stu-id="f7077-783">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="f7077-784">Когда Kestrel настроен на ожидание передачи данных от порта, Kestrel обрабатывает весь трафик для этого порта независимо от заголовка `Host` запросов.</span><span class="sxs-lookup"><span data-stu-id="f7077-784">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="f7077-785">Поэтому обратный прокси-сервер, который может совместно использовать порты, способен пересылать запросы в Kestrel с уникальным IP-адресом и портом.</span><span class="sxs-lookup"><span data-stu-id="f7077-785">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="f7077-786">Даже если обратный прокси-сервер не требуется, его использование может оказаться удобным.</span><span class="sxs-lookup"><span data-stu-id="f7077-786">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="f7077-787">Обратный прокси-сервер:</span><span class="sxs-lookup"><span data-stu-id="f7077-787">A reverse proxy:</span></span>

* <span data-ttu-id="f7077-788">Может ограничить общедоступную контактную зону размещенных на нем приложений.</span><span class="sxs-lookup"><span data-stu-id="f7077-788">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="f7077-789">Предоставляет дополнительный уровень конфигурации и защиты.</span><span class="sxs-lookup"><span data-stu-id="f7077-789">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="f7077-790">Может лучше интегрироваться с существующей инфраструктурой.</span><span class="sxs-lookup"><span data-stu-id="f7077-790">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="f7077-791">Упрощает настройку балансировки нагрузки и безопасных подключений (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f7077-791">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="f7077-792">Сертификат X.509 требуется только обратному прокси-серверу, а сам этот сервер может обмениваться данными с серверами приложения во внутренней сети по обычному протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7077-792">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="f7077-793">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="f7077-793">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="f7077-794">Условия использования Kestrel в приложениях ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7077-794">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="f7077-795">Пакет [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f7077-795">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f7077-796">Шаблоны проектов ASP.NET Core используют Kestrel по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-796">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="f7077-797">В файле *Program.cs* код шаблона вызывает метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, который в фоновом режиме вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>.</span><span class="sxs-lookup"><span data-stu-id="f7077-797">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="f7077-798">Чтобы задать дополнительную конфигурацию после вызова `CreateDefaultBuilder`, вызовите <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="f7077-798">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="f7077-799">Дополнительные сведения о `CreateDefaultBuilder` и построении узла, см. в разделе *Настройка узла*, разделы <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="f7077-799">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="f7077-800">Параметры Kestrel</span><span class="sxs-lookup"><span data-stu-id="f7077-800">Kestrel options</span></span>

<span data-ttu-id="f7077-801">Веб-сервер Kestrel имеет ограничительные параметры конфигурации, которые удобно использовать в развертываниях с выходом в Интернет.</span><span class="sxs-lookup"><span data-stu-id="f7077-801">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="f7077-802">Задать ограничения для свойства <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> в классе <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="f7077-802">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="f7077-803">Свойство `Limits` содержит экземпляр класса <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="f7077-803">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="f7077-804">В следующих примерах используется пространство имен <xref:Microsoft.AspNetCore.Server.Kestrel.Core>.</span><span class="sxs-lookup"><span data-stu-id="f7077-804">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="f7077-805">Параметры Kestrel, которые настраиваются в C# коде в следующих примерах, можно также задать с помощью [поставщика конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="f7077-805">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="f7077-806">Например, поставщик конфигурации файла может загрузить конфигурацию Kestrel из файла *appsettings.JSON* или *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="f7077-806">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="f7077-807">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="f7077-807">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="f7077-808">Настройте Kestrel в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f7077-808">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="f7077-809">Внедрение экземпляра `IConfiguration` в класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="f7077-809">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="f7077-810">В следующем примере предполагается, что введенная конфигурация назначается свойству `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="f7077-810">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="f7077-811">В `Startup.ConfigureServices` загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel:</span><span class="sxs-lookup"><span data-stu-id="f7077-811">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="f7077-812">Настройте Kestrel при сборке узла:</span><span class="sxs-lookup"><span data-stu-id="f7077-812">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="f7077-813">В *Program.cs* загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-813">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="f7077-814">Оба предыдущих подхода работают с любым [поставщиком конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="f7077-814">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="f7077-815">Время ожидания проверки на активность</span><span class="sxs-lookup"><span data-stu-id="f7077-815">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="f7077-816">Получает или задает [время ожидания проверки на активность](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="f7077-816">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="f7077-817">Значение по умолчанию — 2 минуты.</span><span class="sxs-lookup"><span data-stu-id="f7077-817">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="f7077-818">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="f7077-818">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="f7077-819">Максимальное число одновременно открытых подключений TCP для всего приложения можно задать с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="f7077-819">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="f7077-820">Существует отдельный предел по подключениям, измененным с HTTP или HTTPS на другой протокол (например, по запросу WebSocket).</span><span class="sxs-lookup"><span data-stu-id="f7077-820">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="f7077-821">После изменения подключение не учитывается в пределе `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="f7077-821">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="f7077-822">По умолчанию максимальное число подключений не ограничено (null).</span><span class="sxs-lookup"><span data-stu-id="f7077-822">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="f7077-823">максимальный размер текста запроса;</span><span class="sxs-lookup"><span data-stu-id="f7077-823">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="f7077-824">По умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="f7077-824">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="f7077-825">Чтобы переопределить это ограничение в приложении MVC ASP.NET Core, мы рекомендуем использовать атрибут <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> в методе действия:</span><span class="sxs-lookup"><span data-stu-id="f7077-825">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="f7077-826">Приведенный ниже пример показывает, как настроить ограничение для приложения и каждого запроса:</span><span class="sxs-lookup"><span data-stu-id="f7077-826">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="f7077-827">Переопределите параметр для конкретного запроса в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="f7077-827">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="f7077-828">Если приложение пытается настроить ограничение для запроса после того, как оно начало считывать запрос, возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="f7077-828">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="f7077-829">Доступно свойство `IsReadOnly`, указывающее, что свойство `MaxRequestBodySize` находится в состоянии только для чтения и настраивать ограничение слишком поздно.</span><span class="sxs-lookup"><span data-stu-id="f7077-829">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="f7077-830">При запуске приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), который обслуживает [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module), ограничение на размер текста запроса Kestrel будет отключено, так как оно уже задается IIS.</span><span class="sxs-lookup"><span data-stu-id="f7077-830">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="f7077-831">минимальная скорость передачи данных в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="f7077-831">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="f7077-832">Kestrel каждую секунду проверяет, поступают ли данные с указанной скоростью в байтах в секунду.</span><span class="sxs-lookup"><span data-stu-id="f7077-832">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="f7077-833">Если скорость падает ниже минимума, для соединения истекает время ожидания. Льготный период — это количество времени, которое Kestrel дает клиенту на увеличение его скорости отправки до минимального уровня; в течение этого периода скорость не проверяется.</span><span class="sxs-lookup"><span data-stu-id="f7077-833">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="f7077-834">Льготный период помогает избежать разрыва соединений, которые первоначально отправляют данные с небольшой скоростью из-за медленного запуска TCP.</span><span class="sxs-lookup"><span data-stu-id="f7077-834">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="f7077-835">Минимальная скорость по умолчанию составляет 240 байт/с, льготный период равен 5 секундам.</span><span class="sxs-lookup"><span data-stu-id="f7077-835">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="f7077-836">Минимальная скорость также применяется к отклику.</span><span class="sxs-lookup"><span data-stu-id="f7077-836">A minimum rate also applies to the response.</span></span> <span data-ttu-id="f7077-837">Код для задания лимита запросов и лимита откликов различается только наличием `RequestBody` или `Response` в именах свойств и интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="f7077-837">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="f7077-838">Ниже приведен пример, показывающий, как настроить минимальную скорость передачи данных в *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f7077-838">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="f7077-839">Время ожидания для заголовков запросов</span><span class="sxs-lookup"><span data-stu-id="f7077-839">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="f7077-840">Получает или задает максимальное время, которое сервер уделяет получению заголовков запросов.</span><span class="sxs-lookup"><span data-stu-id="f7077-840">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="f7077-841">Значение по умолчанию — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="f7077-841">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="f7077-842">Синхронный ввод-вывод</span><span class="sxs-lookup"><span data-stu-id="f7077-842">Synchronous I/O</span></span>

<span data-ttu-id="f7077-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> определяет, разрешены ли синхронные операции ввода-вывода для запроса и ответа.</span><span class="sxs-lookup"><span data-stu-id="f7077-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="f7077-844">Значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="f7077-844">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f7077-845">Выполнение большого числа заблокированных операций синхронного ввода-вывода может привести к перегрузке пула потоков и зависанию приложения.</span><span class="sxs-lookup"><span data-stu-id="f7077-845">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="f7077-846">Включайте `AllowSynchronousIO`, только если вы используете библиотеку, которая не поддерживает асинхронные операции ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="f7077-846">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="f7077-847">В примере ниже отключаются синхронные операции ввода-вывода:</span><span class="sxs-lookup"><span data-stu-id="f7077-847">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="f7077-848">Сведения о других параметрах и ограничениях Kestrel см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="f7077-848">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="f7077-849">Конфигурация конечной точки</span><span class="sxs-lookup"><span data-stu-id="f7077-849">Endpoint configuration</span></span>

<span data-ttu-id="f7077-850">По умолчанию платформа ASP.NET Core привязана к:</span><span class="sxs-lookup"><span data-stu-id="f7077-850">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="f7077-851">`https://localhost:5001` (если присутствует локальный сертификат разработки)</span><span class="sxs-lookup"><span data-stu-id="f7077-851">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="f7077-852">Укажите URL-адреса с помощью следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="f7077-852">Specify URLs using the:</span></span>

* <span data-ttu-id="f7077-853">Переменная среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="f7077-853">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="f7077-854">Аргументы командной строки `--urls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-854">`--urls` command-line argument.</span></span>
* <span data-ttu-id="f7077-855">Ключ конфигурации узла `urls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-855">`urls` host configuration key.</span></span>
* <span data-ttu-id="f7077-856">Метод расширения `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-856">`UseUrls` extension method.</span></span>

<span data-ttu-id="f7077-857">Значение, указанное с помощью этих подходов, может быть одной или несколькими конечными точками HTTP и HTTPS (HTTPS при наличии сертификата по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="f7077-857">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="f7077-858">Настройте значение в виде списка с разделением точкой с запятой (например, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="f7077-858">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="f7077-859">Дополнительные сведения о таких подходах см. в разделах [URL-адреса сервера](xref:fundamentals/host/web-host#server-urls) и [Переопределение конфигурации](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="f7077-859">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="f7077-860">Сертификат разработки создается, когда:</span><span class="sxs-lookup"><span data-stu-id="f7077-860">A development certificate is created:</span></span>

* <span data-ttu-id="f7077-861">установлен [пакет SDK для .NET Core](/dotnet/core/sdk);</span><span class="sxs-lookup"><span data-stu-id="f7077-861">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="f7077-862">используется [средство dev-certs](xref:aspnetcore-2.1#https) для создания сертификата.</span><span class="sxs-lookup"><span data-stu-id="f7077-862">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="f7077-863">В некоторых браузерах требуется явное разрешение доверять локальному сертификату разработки.</span><span class="sxs-lookup"><span data-stu-id="f7077-863">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="f7077-864">Шаблоны проектов настраивают приложения так, чтобы они запускались на базе HTTPS по умолчанию и включали [поддержку перенаправления HTTPS и HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="f7077-864">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="f7077-865">Вызовите методы <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> или <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> из <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>, чтобы настроить префиксы URL-адресов и порты для Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-865">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="f7077-866">`UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` и переменная среды `ASPNETCORE_URLS` тоже работают, однако на них распространяются ограничения, указанные далее в этой статье (для конфигурации конечной точки HTTPS требуется сертификат по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="f7077-866">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="f7077-867">Конфигурация `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="f7077-867">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="f7077-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="f7077-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="f7077-869">Указывает конфигурацию `Action` для выполнения каждой заданной конечной точки.</span><span class="sxs-lookup"><span data-stu-id="f7077-869">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="f7077-870">Если вызвать `ConfigureEndpointDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.</span><span class="sxs-lookup"><span data-stu-id="f7077-870">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="f7077-871">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-871">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="f7077-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="f7077-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="f7077-873">Указывает конфигурацию `Action` для выполнения каждой конечной точки HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f7077-873">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="f7077-874">Если вызвать `ConfigureHttpsDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.</span><span class="sxs-lookup"><span data-stu-id="f7077-874">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="f7077-875">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-875">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="f7077-876">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="f7077-876">Configure(IConfiguration)</span></span>

<span data-ttu-id="f7077-877">Создает загрузчик конфигурации для настройки Kestrel, который принимает <xref:Microsoft.Extensions.Configuration.IConfiguration> в качестве входных данных.</span><span class="sxs-lookup"><span data-stu-id="f7077-877">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="f7077-878">Для Kestrel конфигурация должна быть ограничена разделом конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f7077-878">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="f7077-879">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="f7077-879">ListenOptions.UseHttps</span></span>

<span data-ttu-id="f7077-880">Настройте Kestrel для использования протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f7077-880">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="f7077-881">Расширения `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="f7077-881">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="f7077-882">`UseHttps`. Настройте Kestrel для использования протокола HTTPS с сертификатом по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-882">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="f7077-883">Создает исключение, если сертификат по умолчанию не настроен.</span><span class="sxs-lookup"><span data-stu-id="f7077-883">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="f7077-884">Параметры `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="f7077-884">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="f7077-885">`filename` — это путь и имя файла сертификата, связанного с каталогом, где находятся файлы содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="f7077-885">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="f7077-886">`password` — это пароль для доступа к данным сертификата X.509.</span><span class="sxs-lookup"><span data-stu-id="f7077-886">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="f7077-887">`configureOptions` — это `Action` для настройки `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="f7077-887">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="f7077-888">Возвращает `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="f7077-888">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="f7077-889">`storeName` — это хранилище сертификатов, из которого выполняется загрузка сертификата.</span><span class="sxs-lookup"><span data-stu-id="f7077-889">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="f7077-890">`subject` — это имя субъекта для сертификата.</span><span class="sxs-lookup"><span data-stu-id="f7077-890">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="f7077-891">`allowInvalid` указывает, следует ли учитывать недопустимые сертификаты, например самозаверяющие сертификаты.</span><span class="sxs-lookup"><span data-stu-id="f7077-891">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="f7077-892">`location` — это расположение хранилища, из которого загружается сертификат.</span><span class="sxs-lookup"><span data-stu-id="f7077-892">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="f7077-893">`serverCertificate` — это сертификат X.509.</span><span class="sxs-lookup"><span data-stu-id="f7077-893">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="f7077-894">В рабочей среде необходимо явно настроить HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f7077-894">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="f7077-895">Как минимум необходимо указать сертификат по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-895">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="f7077-896">Поддерживаемые конфигурации, описанные далее:</span><span class="sxs-lookup"><span data-stu-id="f7077-896">Supported configurations described next:</span></span>

* <span data-ttu-id="f7077-897">Отсутствие конфигурации</span><span class="sxs-lookup"><span data-stu-id="f7077-897">No configuration</span></span>
* <span data-ttu-id="f7077-898">Замена сертификата по умолчанию из конфигурации</span><span class="sxs-lookup"><span data-stu-id="f7077-898">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="f7077-899">Изменение значений по умолчанию в коде</span><span class="sxs-lookup"><span data-stu-id="f7077-899">Change the defaults in code</span></span>

<span data-ttu-id="f7077-900">*Отсутствие конфигурации*</span><span class="sxs-lookup"><span data-stu-id="f7077-900">*No configuration*</span></span>

<span data-ttu-id="f7077-901">Kestrel ожидает передачи данных через `http://localhost:5000` и `https://localhost:5001` (если доступен сертификат по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="f7077-901">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="f7077-902">*Замена сертификата по умолчанию из конфигурации*</span><span class="sxs-lookup"><span data-stu-id="f7077-902">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="f7077-903">`CreateDefaultBuilder` по умолчанию вызывает `Configure(context.Configuration.GetSection("Kestrel"))`, чтобы загрузить конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-903">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="f7077-904">Kestrel имеет доступ к схеме конфигурации параметров приложения HTTPS по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-904">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="f7077-905">Настройте несколько конечных точек, включая URL-адреса и сертификаты для использования, либо из файла на диске, либо из хранилища сертификатов.</span><span class="sxs-lookup"><span data-stu-id="f7077-905">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="f7077-906">В следующем примере *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f7077-906">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="f7077-907">Установите для **AllowInvalid** значение `true`, чтобы разрешить использование недопустимых сертификатов (например, самозаверяющих сертификатов).</span><span class="sxs-lookup"><span data-stu-id="f7077-907">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="f7077-908">Любая конечная точка HTTPS, которая не указывает сертификат (**HttpsDefaultCert** в следующем примере), будет использовать сертификат, определенный в разделе **Certificates** (Сертификаты) > **Default** (По умолчанию), или сертификат разработки.</span><span class="sxs-lookup"><span data-stu-id="f7077-908">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="f7077-909">Вместо использования параметров **Path** и **Password** для узла сертификата можно указать сертификат с помощью полей хранилища сертификатов.</span><span class="sxs-lookup"><span data-stu-id="f7077-909">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="f7077-910">Например, сертификат из раздела **Сертификаты** > **По умолчанию** можно указать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f7077-910">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="f7077-911">Примечания к схеме.</span><span class="sxs-lookup"><span data-stu-id="f7077-911">Schema notes:</span></span>

* <span data-ttu-id="f7077-912">Регистр букв в именах конечных точек не учитывается.</span><span class="sxs-lookup"><span data-stu-id="f7077-912">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="f7077-913">Например, `HTTPS` и `Https` являются допустимыми.</span><span class="sxs-lookup"><span data-stu-id="f7077-913">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="f7077-914">Параметр `Url` является обязательным для каждой конечной точки.</span><span class="sxs-lookup"><span data-stu-id="f7077-914">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="f7077-915">Формат этого параметра такой же, как для параметра конфигурации `Urls` верхнего уровня, только он ограничен одиночным значением.</span><span class="sxs-lookup"><span data-stu-id="f7077-915">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="f7077-916">Эти конечные точки заменяют конечные точки, определенные в конфигурации `Urls` верхнего уровня, а не дополняют их.</span><span class="sxs-lookup"><span data-stu-id="f7077-916">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="f7077-917">Конечные точки, определенные в коде через `Listen`, объединяются с конечными точками, определенными в разделе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f7077-917">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="f7077-918">Раздел `Certificate` является необязательным.</span><span class="sxs-lookup"><span data-stu-id="f7077-918">The `Certificate` section is optional.</span></span> <span data-ttu-id="f7077-919">Если раздел `Certificate` не указан, используются значения по умолчанию, определенные в предыдущих сценариях.</span><span class="sxs-lookup"><span data-stu-id="f7077-919">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="f7077-920">Если значений по умолчанию нет, сервер выдает исключение и не запускается.</span><span class="sxs-lookup"><span data-stu-id="f7077-920">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="f7077-921">Раздел `Certificate` поддерживает сертификаты **Path**&ndash;**Password** и **Subject**&ndash;**Store**.</span><span class="sxs-lookup"><span data-stu-id="f7077-921">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="f7077-922">Таким образом, можно определить любое количество конечных точек, если это не приводит к конфликту портов.</span><span class="sxs-lookup"><span data-stu-id="f7077-922">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="f7077-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` возвращает `KestrelConfigurationLoader` с методом `.Endpoint(string name, listenOptions => { })`, который может использоваться в качестве дополнения для параметров настроенной конечной точки:</span><span class="sxs-lookup"><span data-stu-id="f7077-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="f7077-924">Можно обратиться напрямую к `KestrelServerOptions.ConfigurationLoader`, чтобы и далее выполнять итерацию с существующим загрузчиком, например, предоставленным <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="f7077-924">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="f7077-925">Раздел конфигурации для каждой конечной точки доступен в параметрах в методе `Endpoint`, чтобы можно было прочитать пользовательские параметры.</span><span class="sxs-lookup"><span data-stu-id="f7077-925">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="f7077-926">Можно загрузить несколько конфигураций, снова вызвав `options.Configure(context.Configuration.GetSection("{SECTION}"))` с другим разделом.</span><span class="sxs-lookup"><span data-stu-id="f7077-926">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="f7077-927">Используется только последняя конфигурация, если явным образом не вызвать `Load` в предыдущих экземплярах.</span><span class="sxs-lookup"><span data-stu-id="f7077-927">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="f7077-928">Метапакет не вызывает `Load`, чтобы можно было заменить его раздел конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-928">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="f7077-929">`KestrelConfigurationLoader` отражает семейство API `Listen` из `KestrelServerOptions` как перегрузки `Endpoint`, чтобы можно было настроить конечные точки кода и конфигурации в одном месте.</span><span class="sxs-lookup"><span data-stu-id="f7077-929">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="f7077-930">Эти перегрузки не используют имена и используют только параметры по умолчанию из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f7077-930">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="f7077-931">*Изменение значений по умолчанию в коде*</span><span class="sxs-lookup"><span data-stu-id="f7077-931">*Change the defaults in code*</span></span>

<span data-ttu-id="f7077-932">Можно использовать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults` для изменения параметров по умолчанию для `ListenOptions` и `HttpsConnectionAdapterOptions`, включая переопределение сертификата по умолчанию, указанного в предыдущем сценарии.</span><span class="sxs-lookup"><span data-stu-id="f7077-932">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="f7077-933">Необходимо вызвать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults`, прежде чем настраивать конечные точки.</span><span class="sxs-lookup"><span data-stu-id="f7077-933">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="f7077-934">*Поддержка Kestrel для SNI*</span><span class="sxs-lookup"><span data-stu-id="f7077-934">*Kestrel support for SNI*</span></span>

<span data-ttu-id="f7077-935">Можно использовать [указание имени сервера (SNI)](https://tools.ietf.org/html/rfc6066#section-3) для размещения нескольких доменов в одном IP-адресе и порте.</span><span class="sxs-lookup"><span data-stu-id="f7077-935">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="f7077-936">Для использования SNI клиент отправляет имя узла для безопасного сеанса серверу во время подтверждения TLS, чтобы сервер предоставил правильный сертификат.</span><span class="sxs-lookup"><span data-stu-id="f7077-936">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="f7077-937">Клиент использует предоставленный сертификат для зашифрованного соединения с сервером во время безопасного сеанса, который следует после подтверждения TLS.</span><span class="sxs-lookup"><span data-stu-id="f7077-937">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="f7077-938">Kestrel поддерживает SNI через обратный вызов `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="f7077-938">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="f7077-939">Функция обратного вызова используется один раз за подключение, чтобы приложение проверило имя узла и выбрало соответствующий сертификат.</span><span class="sxs-lookup"><span data-stu-id="f7077-939">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="f7077-940">Поддержка SNI требует:</span><span class="sxs-lookup"><span data-stu-id="f7077-940">SNI support requires:</span></span>

* <span data-ttu-id="f7077-941">Запуск на целевой платформе `netcoreapp2.1` или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="f7077-941">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="f7077-942">В `net461` или более поздней версии обратный вызов выполняется, но `name` всегда имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="f7077-942">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="f7077-943">`name` также имеет значение `null`, если клиент не предоставляет параметр имени узла при подтверждении TLS.</span><span class="sxs-lookup"><span data-stu-id="f7077-943">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="f7077-944">Все веб-сайты выполняются на одном и том же экземпляре Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-944">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="f7077-945">Kestrel не поддерживает совместное использование IP-адреса и порта на нескольких экземплярах без обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="f7077-945">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="f7077-946">Ведение журнала подключения</span><span class="sxs-lookup"><span data-stu-id="f7077-946">Connection logging</span></span>

<span data-ttu-id="f7077-947">Вызовите <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>, чтобы выдать журналы уровня отладки для обмена данными на уровне байтов в рамках подключения.</span><span class="sxs-lookup"><span data-stu-id="f7077-947">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="f7077-948">Ведение журнала подключения полезно для устранения неполадок, связанных с низкоуровневым взаимодействием, например при TLS-шифровании и работе за прокси-серверами.</span><span class="sxs-lookup"><span data-stu-id="f7077-948">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="f7077-949">Если `UseConnectionLogging` поместить перед `UseHttps`, в журнале регистрируется зашифрованный трафик.</span><span class="sxs-lookup"><span data-stu-id="f7077-949">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="f7077-950">Если `UseConnectionLogging` поместить после `UseHttps`, в журнале регистрируется расшифрованный трафик.</span><span class="sxs-lookup"><span data-stu-id="f7077-950">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="f7077-951">Привязка к TCP-сокету</span><span class="sxs-lookup"><span data-stu-id="f7077-951">Bind to a TCP socket</span></span>

<span data-ttu-id="f7077-952">Метод <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> выполняет привязку к TCP-сокету, а лямбда-выражение параметров позволяет настроить конфигурацию сертификата X.509:</span><span class="sxs-lookup"><span data-stu-id="f7077-952">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="f7077-953">В этом примере настраивается HTTPS для конечной точки с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="f7077-953">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="f7077-954">С помощью этого API можно настроить и другие параметры Kestrel для отдельных конечных точек.</span><span class="sxs-lookup"><span data-stu-id="f7077-954">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="f7077-955">Привязка к сокету UNIX</span><span class="sxs-lookup"><span data-stu-id="f7077-955">Bind to a Unix socket</span></span>

<span data-ttu-id="f7077-956">Вы можете прослушивать сокет UNIX с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, чтобы улучшить производительность Nginx, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="f7077-956">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="f7077-957">В файле конфигурации Nginx установите для записи `server` > `location` > `proxy_pass` значение `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="f7077-957">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="f7077-958">`{KESTREL SOCKET}` — это имя сокета, предоставленного для <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (как `kestrel-test.sock` в предыдущем примере).</span><span class="sxs-lookup"><span data-stu-id="f7077-958">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="f7077-959">Убедитесь, что сокет доступен для записи Nginx (например, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="f7077-959">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="f7077-960">Порт 0</span><span class="sxs-lookup"><span data-stu-id="f7077-960">Port 0</span></span>

<span data-ttu-id="f7077-961">Если указать номер порта `0`, Kestrel динамически привязывается к доступному порту.</span><span class="sxs-lookup"><span data-stu-id="f7077-961">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="f7077-962">Следующий пример показывает, как определить, к какому порту фактически привязан Kestrel во время выполнения:</span><span class="sxs-lookup"><span data-stu-id="f7077-962">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="f7077-963">Когда приложение выполняется, в выходных данных в окне консоли указывается динамический порт, по которому можно связаться с приложением:</span><span class="sxs-lookup"><span data-stu-id="f7077-963">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="f7077-964">Ограничения</span><span class="sxs-lookup"><span data-stu-id="f7077-964">Limitations</span></span>

<span data-ttu-id="f7077-965">Настройте конечные точки с помощью следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="f7077-965">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="f7077-966">Аргументы командной строки `--urls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-966">`--urls` command-line argument</span></span>
* <span data-ttu-id="f7077-967">Ключ конфигурации узла `urls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-967">`urls` host configuration key</span></span>
* <span data-ttu-id="f7077-968">Переменная среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="f7077-968">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="f7077-969">Эти методы удобны, если нужно, чтобы код работал с серверами, отличными от Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f7077-969">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="f7077-970">Не забывайте о следующих ограничениях.</span><span class="sxs-lookup"><span data-stu-id="f7077-970">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="f7077-971">С этими подходами нельзя использовать HTTPS, если в конфигурации конечной точки HTTPS не предоставлен сертификат по умолчанию (например, с помощью конфигурации `KestrelServerOptions` или файла конфигурации, как показано выше в этом разделе).</span><span class="sxs-lookup"><span data-stu-id="f7077-971">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="f7077-972">Если подходы `Listen` и `UseUrls` используются одновременно, конечные точки `Listen` переопределяют конечные точки `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-972">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="f7077-973">Конфигурация конечной точки IIS</span><span class="sxs-lookup"><span data-stu-id="f7077-973">IIS endpoint configuration</span></span>

<span data-ttu-id="f7077-974">При использовании служб IIS привязки URL-адресов для IIS переопределяют привязки, заданные `Listen` или `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-974">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="f7077-975">Дополнительные сведения см. в статье [Модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="f7077-975">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="f7077-976">Конфигурация транспорта</span><span class="sxs-lookup"><span data-stu-id="f7077-976">Transport configuration</span></span>

<span data-ttu-id="f7077-977">После выпуска ASP.NET Core 2.1 транспорт Kestrel по умолчанию основан не на Libuv, а на управляемых сокетах.</span><span class="sxs-lookup"><span data-stu-id="f7077-977">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="f7077-978">Это критическое изменение для приложений ASP.NET Core 2.0, которые обновляются до версии 2.1, если они вызывают <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> и зависят от одного из следующих пакетов:</span><span class="sxs-lookup"><span data-stu-id="f7077-978">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="f7077-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (прямая ссылка на пакет)</span><span class="sxs-lookup"><span data-stu-id="f7077-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="f7077-980">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="f7077-980">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="f7077-981">Для проектов, где требуется использовать Libuv:</span><span class="sxs-lookup"><span data-stu-id="f7077-981">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="f7077-982">Добавляют зависимость для пакета [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) к файлу проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="f7077-982">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="f7077-983">Вызов <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="f7077-983">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="f7077-984">Префиксы URL-адресов</span><span class="sxs-lookup"><span data-stu-id="f7077-984">URL prefixes</span></span>

<span data-ttu-id="f7077-985">Если вы используете `UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` или переменную среды `ASPNETCORE_URLS`, префиксы URL-адресов могут иметь любой из указанных ниже форматов.</span><span class="sxs-lookup"><span data-stu-id="f7077-985">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="f7077-986">Допустимы только префиксы URL-адресов HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7077-986">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="f7077-987">Kestrel не поддерживает HTTP при настройке привязок URL-адресов с помощью `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="f7077-987">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="f7077-988">IPv4-адрес с номером порта</span><span class="sxs-lookup"><span data-stu-id="f7077-988">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="f7077-989">`0.0.0.0` является особым случаем, соответствующим привязке ко всем IPv4-адресам.</span><span class="sxs-lookup"><span data-stu-id="f7077-989">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="f7077-990">IPv6-адрес с номером порта</span><span class="sxs-lookup"><span data-stu-id="f7077-990">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="f7077-991">`[::]` является IPv6-аналогом IPv4-адреса `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="f7077-991">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="f7077-992">Имя узла с номером порта</span><span class="sxs-lookup"><span data-stu-id="f7077-992">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="f7077-993">Имена узлов, `*` и `+`, не являются особыми.</span><span class="sxs-lookup"><span data-stu-id="f7077-993">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="f7077-994">Все, что не распознается как допустимый IP-адрес или `localhost`, привязывается ко всем IP-адресам IPv4 и IPv6.</span><span class="sxs-lookup"><span data-stu-id="f7077-994">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="f7077-995">Чтобы привязать разные имена узлов к разным приложениям ASP.NET Core по одному порту, используйте [HTTP.sys](xref:fundamentals/servers/httpsys) или обратный прокси-сервер, такой как IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="f7077-995">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="f7077-996">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="f7077-996">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="f7077-997">Имя узла `localhost` с номером порта или IP-адрес замыкания на себя с номером порта</span><span class="sxs-lookup"><span data-stu-id="f7077-997">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="f7077-998">Когда указан `localhost`, Kestrel пытается привязаться к обоим интерфейсам замыкания на себя IPv4 и IPv6.</span><span class="sxs-lookup"><span data-stu-id="f7077-998">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="f7077-999">Если запрошенный порт уже используется другой службой в одном из интерфейсов замыкания на себя, Kestrel не запускается.</span><span class="sxs-lookup"><span data-stu-id="f7077-999">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="f7077-1000">Если один из интерфейсов замыкания на себя недоступен по любой другой причине (чаще всего, это отсутствие поддержки IPv6), Kestrel заносит в журнал предупреждение.</span><span class="sxs-lookup"><span data-stu-id="f7077-1000">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="f7077-1001">Фильтрация узлов</span><span class="sxs-lookup"><span data-stu-id="f7077-1001">Host filtering</span></span>

<span data-ttu-id="f7077-1002">Хотя Kestrel поддерживает конфигурации с использованием префиксов, такие как `http://example.com:5000`, Kestrel не учитывает имя узла.</span><span class="sxs-lookup"><span data-stu-id="f7077-1002">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="f7077-1003">Узел `localhost` является особым случаем, используемым для привязки к адресам замыкания на себя.</span><span class="sxs-lookup"><span data-stu-id="f7077-1003">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="f7077-1004">Любой узел, отличный от явного IP-адреса, привязывается ко всем общедоступным IP-адресам.</span><span class="sxs-lookup"><span data-stu-id="f7077-1004">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="f7077-1005">Заголовки `Host` не проверяются.</span><span class="sxs-lookup"><span data-stu-id="f7077-1005">`Host` headers aren't validated.</span></span>

<span data-ttu-id="f7077-1006">В качестве обходного решения используйте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="f7077-1006">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="f7077-1007">ПО промежуточного слоя фильтрации узла предоставляется пакетом [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), который входит в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 или 2.2).</span><span class="sxs-lookup"><span data-stu-id="f7077-1007">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="f7077-1008">ПО промежуточного слоя добавляется в <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, который вызывает <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="f7077-1008">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="f7077-1009">ПО промежуточного слоя фильтрации узлов отключено по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f7077-1009">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="f7077-1010">Чтобы включить ПО промежуточного слоя, определите ключ `AllowedHosts` в *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="f7077-1010">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="f7077-1011">Значение представляет собой разделенный точками с запятой список имен узлов без номеров портов:</span><span class="sxs-lookup"><span data-stu-id="f7077-1011">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="f7077-1012">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f7077-1012">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="f7077-1013">[ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer) имеет также параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="f7077-1013">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="f7077-1014">ПО промежуточного слоя перенаправления заголовков и ПО промежуточного слоя фильтрации узлов обладают сходными возможностями для различных сценариев.</span><span class="sxs-lookup"><span data-stu-id="f7077-1014">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="f7077-1015">Параметр `AllowedHosts` с ПО промежуточного слоя перенаправления заголовков подходит для случаев, когда заголовок `Host` не сохраняется при переадресации запросов с помощью обратного прокси-сервера или подсистемы балансировки нагрузки.</span><span class="sxs-lookup"><span data-stu-id="f7077-1015">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="f7077-1016">Параметр `AllowedHosts` с ПО промежуточного слоя фильтрации узлов подходит для случаев, когда Kestrel используется в качестве общедоступного пограничного сервера или если заголовок `Host` пересылается напрямую.</span><span class="sxs-lookup"><span data-stu-id="f7077-1016">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="f7077-1017">Дополнительные сведения о ПО промежуточного слоя перенаправления заголовков см. в статье <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="f7077-1017">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="f7077-1018">Очистка запросов HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="f7077-1018">HTTP/1.1 request draining</span></span>

<span data-ttu-id="f7077-1019">Открытие HTTP-соединений занимает много времени.</span><span class="sxs-lookup"><span data-stu-id="f7077-1019">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="f7077-1020">Для протокола HTTPS это также требует больших ресурсов.</span><span class="sxs-lookup"><span data-stu-id="f7077-1020">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="f7077-1021">Поэтому Kestrel пытается повторно использовать подключения по протоколу HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="f7077-1021">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="f7077-1022">Чтобы разрешить повторное использование соединения, текст запроса должен быть полностью использован.</span><span class="sxs-lookup"><span data-stu-id="f7077-1022">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="f7077-1023">Приложение не всегда использует текст запроса, например запросы `POST`, в которых сервер возвращает ответ перенаправления или 404.</span><span class="sxs-lookup"><span data-stu-id="f7077-1023">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="f7077-1024">В случае перенаправления `POST`:</span><span class="sxs-lookup"><span data-stu-id="f7077-1024">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="f7077-1025">Возможно, клиент уже отправил часть данных `POST`.</span><span class="sxs-lookup"><span data-stu-id="f7077-1025">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="f7077-1026">Сервер записывает ответ 301.</span><span class="sxs-lookup"><span data-stu-id="f7077-1026">The server writes the 301 response.</span></span>
* <span data-ttu-id="f7077-1027">Соединение нельзя использовать для нового запроса, пока не будут полностью прочитаны данные `POST` из предыдущего текста запроса.</span><span class="sxs-lookup"><span data-stu-id="f7077-1027">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="f7077-1028">Kestrel пытается очистить текст запроса.</span><span class="sxs-lookup"><span data-stu-id="f7077-1028">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="f7077-1029">Очистка текста запроса означает чтение и отмену данных без их обработки.</span><span class="sxs-lookup"><span data-stu-id="f7077-1029">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="f7077-1030">Процесс очистки позволяет найти баланс между возможностью повторного использования соединения и временем, которое требуется для очистки оставшихся данных.</span><span class="sxs-lookup"><span data-stu-id="f7077-1030">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="f7077-1031">Время ожидания очистки составляет 5 секунд. Этот параметр нельзя изменить.</span><span class="sxs-lookup"><span data-stu-id="f7077-1031">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="f7077-1032">Если все данные, указанные в заголовке `Content-Length` или `Transfer-Encoding`, не были считаны до истечения времени ожидания, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="f7077-1032">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="f7077-1033">Иногда может потребоваться немедленно завершить запрос до или после записи ответа.</span><span class="sxs-lookup"><span data-stu-id="f7077-1033">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="f7077-1034">Например, клиенты могут иметь ограничения на данные, поэтому ограничение передаваемых данных может иметь приоритет.</span><span class="sxs-lookup"><span data-stu-id="f7077-1034">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="f7077-1035">В таких случаях для завершения запроса вызовите [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) из контроллера, страницы Razor или ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="f7077-1035">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="f7077-1036">Вызов `Abort` имеет определенные недостатки.</span><span class="sxs-lookup"><span data-stu-id="f7077-1036">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="f7077-1037">Создание новых подключений может выполняться очень медленно и требовать много ресурсов.</span><span class="sxs-lookup"><span data-stu-id="f7077-1037">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="f7077-1038">Нет никакой гарантии, что клиент прочитал ответ перед закрытием соединения.</span><span class="sxs-lookup"><span data-stu-id="f7077-1038">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="f7077-1039">Вызов `Abort` следует использовать редко и только для серьезных, а не распространенных ошибок.</span><span class="sxs-lookup"><span data-stu-id="f7077-1039">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="f7077-1040">Вызывайте `Abort`, только когда нужно решить конкретную проблему.</span><span class="sxs-lookup"><span data-stu-id="f7077-1040">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="f7077-1041">Например, вызовите `Abort`, если вредоносные клиенты пытаются выполнить операцию `POST` с данными или если в клиентском коде есть ошибка, вызывающая большие или многочисленные запросы.</span><span class="sxs-lookup"><span data-stu-id="f7077-1041">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="f7077-1042">Не вызывайте `Abort` для распространенных ошибок, таких как HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="f7077-1042">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="f7077-1043">Вызов [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) перед вызовом `Abort` гарантирует, что сервер завершит запись ответа.</span><span class="sxs-lookup"><span data-stu-id="f7077-1043">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="f7077-1044">Однако поведение клиента не предсказуемо. Он может не считать ответ, прежде чем подключение будет прервано.</span><span class="sxs-lookup"><span data-stu-id="f7077-1044">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="f7077-1045">Этот процесс отличается для HTTP/2, так как протокол поддерживает прерывание отдельных потоков запросов без закрытия соединения.</span><span class="sxs-lookup"><span data-stu-id="f7077-1045">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="f7077-1046">5-секундное время ожидания очистки не применяется.</span><span class="sxs-lookup"><span data-stu-id="f7077-1046">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="f7077-1047">Если после завершения ответа сервер содержит непрочтенные данные текста запроса, он отправляет кадр HTTP/2 RST.</span><span class="sxs-lookup"><span data-stu-id="f7077-1047">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="f7077-1048">Дополнительные кадры данных текста запроса игнорируются.</span><span class="sxs-lookup"><span data-stu-id="f7077-1048">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="f7077-1049">По возможности клиентам лучше использовать заголовок запроса [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) и дожидаться ответа сервера перед началом отправки текста запроса.</span><span class="sxs-lookup"><span data-stu-id="f7077-1049">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="f7077-1050">Это дает клиенту возможность проверить ответ и прервать операцию перед отправкой ненужных данных.</span><span class="sxs-lookup"><span data-stu-id="f7077-1050">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7077-1051">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f7077-1051">Additional resources</span></span>

* <span data-ttu-id="f7077-1052">При использовании сокетов UNIX в Linux сокет не удаляется автоматически по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="f7077-1052">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="f7077-1053">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="f7077-1053">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="f7077-1054">RFC 7230: синтаксис и маршрутизация сообщений (раздел 5.4: узел)</span><span class="sxs-lookup"><span data-stu-id="f7077-1054">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
