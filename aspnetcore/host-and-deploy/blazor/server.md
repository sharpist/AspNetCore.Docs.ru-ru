---
title: Размещение и развертывание серверного приложения Blazor с помощью ASP.NET Core
author: guardrex
description: Сведения о размещении и развертывании серверного приложения Blazor с помощью ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 8c06d3a4d0d75a3e2fd9f699af38a23833fa8bce
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84419948"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="d56b4-103">Размещение и развертывание серверного приложения Blazor</span><span class="sxs-lookup"><span data-stu-id="d56b4-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="d56b4-104">Авторы: [Люк Лэтем](https://github.com/guardrex), [Рэйнер Стропек](https://www.timecockpit.com) и [Дэниэл Рот](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d56b4-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="d56b4-105">Значения конфигурации узла</span><span class="sxs-lookup"><span data-stu-id="d56b4-105">Host configuration values</span></span>

<span data-ttu-id="d56b4-106">[Серверные приложения Blazor](xref:blazor/hosting-models#blazor-server) могут принимать [значения конфигурации универсального узла](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="d56b4-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="d56b4-107">Развертывание</span><span class="sxs-lookup"><span data-stu-id="d56b4-107">Deployment</span></span>

<span data-ttu-id="d56b4-108">Если используется [модель размещения на сервере Blazor](xref:blazor/hosting-models#blazor-server), Blazor выполняется на сервере из приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d56b4-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="d56b4-109">Обновление элементов пользовательского интерфейса, обработка событий и вызовы JavaScript обрабатываются через подключение [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="d56b4-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="d56b4-110">Необходим веб-сервер, позволяющий размещать приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d56b4-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="d56b4-111">Visual Studio содержит шаблон проекта **Серверное приложение Blazor** (шаблон `blazorserverside` при использовании команды [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="d56b4-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="d56b4-112">Масштабируемость</span><span class="sxs-lookup"><span data-stu-id="d56b4-112">Scalability</span></span>

<span data-ttu-id="d56b4-113">Планируйте развертывание так, чтобы доступная инфраструктура максимально эффективно использовалась серверным приложением Blazor.</span><span class="sxs-lookup"><span data-stu-id="d56b4-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="d56b4-114">Вопросы, связанные с масштабируемостью серверных приложений Blazor, рассматриваются в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="d56b4-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="d56b4-115">[Статья со сведениями о серверных приложениях Blazor](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="d56b4-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="d56b4-116">Сервер развертывания</span><span class="sxs-lookup"><span data-stu-id="d56b4-116">Deployment server</span></span>

<span data-ttu-id="d56b4-117">В контексте масштабируемости отдельного сервера (вертикальное масштабирование) доступная приложению память с большой вероятностью будет первым ресурсом, расходуемым приложением при изменении потребностей пользователей.</span><span class="sxs-lookup"><span data-stu-id="d56b4-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="d56b4-118">Объем доступной памяти на сервере влияет на:</span><span class="sxs-lookup"><span data-stu-id="d56b4-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="d56b4-119">число активных каналов, которые может поддерживать сервер;</span><span class="sxs-lookup"><span data-stu-id="d56b4-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="d56b4-120">задержку пользовательского интерфейса в клиенте.</span><span class="sxs-lookup"><span data-stu-id="d56b4-120">UI latency on the client.</span></span>

<span data-ttu-id="d56b4-121">Рекомендации по созданию безопасных и масштабируемых серверных приложений Blazor см. здесь: <xref:security/blazor/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="d56b4-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server/threat-mitigation>.</span></span>

<span data-ttu-id="d56b4-122">Каждый канал использует около 250 КБ памяти для минималистичного приложения в стиле *Hello World*.</span><span class="sxs-lookup"><span data-stu-id="d56b4-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="d56b4-123">Размер канала зависит от кода приложения и требований к обслуживанию состояний для каждого компонента.</span><span class="sxs-lookup"><span data-stu-id="d56b4-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="d56b4-124">Мы рекомендуем определять требования к ресурсам во время разработки приложения и инфраструктуры, но при планировании цели развертывания можно использовать следующие базовые показатели: если вы предполагаете, что приложение будет одновременно поддерживать 5000 пользователей, выделите для приложения по меньшей мере 1,3 ГБ серверной памяти (или около 273 КБ на пользователя).</span><span class="sxs-lookup"><span data-stu-id="d56b4-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="d56b4-125">Конфигурация SignalR</span><span class="sxs-lookup"><span data-stu-id="d56b4-125">SignalR configuration</span></span>

<span data-ttu-id="d56b4-126">Серверные приложения Blazor используют службу SignalR в ASP.NET Core для обмена данными с браузером.</span><span class="sxs-lookup"><span data-stu-id="d56b4-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="d56b4-127">[Условия размещения и масштабирования SignalR](xref:signalr/publish-to-azure-web-app) применяются и к серверным приложениям Blazor.</span><span class="sxs-lookup"><span data-stu-id="d56b4-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="d56b4-128"> лучше всего работает с WebSocket в качестве транспортного механизма SignalR благодаря низкой задержке, надежности и [безопасности](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="d56b4-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="d56b4-129">Продолжительный опрос используется службой SignalR, если подключения WebSocket недоступны или если приложение явно настроено на применение продолжительного опроса.</span><span class="sxs-lookup"><span data-stu-id="d56b4-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="d56b4-130">При развертывании Службы приложений Azure настройте приложение на использование WebSocket в параметрах службы на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="d56b4-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="d56b4-131">Подробные сведения о настройке приложения для Службы приложений Azure см. в статье с [рекомендациями по публикации приложения SignalR](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="d56b4-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="d56b4-132">Служба Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="d56b4-132">Azure SignalR Service</span></span>

<span data-ttu-id="d56b4-133">Мы рекомендуем использовать для серверных приложений Blazor[службу Azure SignalR](/azure/azure-signalr).</span><span class="sxs-lookup"><span data-stu-id="d56b4-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="d56b4-134">Она позволяет вертикально масштабировать серверные приложения Blazor для одновременного использования большого числа подключений SignalR.</span><span class="sxs-lookup"><span data-stu-id="d56b4-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="d56b4-135">Кроме того, глобальный охват службы SignalR и ее высокопроизводительные центры обработки данных обеспечивают низкую задержку благодаря доступности в разных регионах.</span><span class="sxs-lookup"><span data-stu-id="d56b4-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="d56b4-136">Чтобы настроить приложение (и при необходимости подготовить к работе) для службы Azure SignalR, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="d56b4-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="d56b4-137">Включите в службе поддержку *прикрепленных сеансов*, когда клиенты [перенаправляются обратно на тот же сервер при предварительной отрисовке](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="d56b4-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="d56b4-138">Установите параметр или значение конфигурации `ServerStickyMode` равным `Required`.</span><span class="sxs-lookup"><span data-stu-id="d56b4-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="d56b4-139">Как правило, приложение создает конфигурацию, используя **один** из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="d56b4-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="d56b4-140">`Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d56b4-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="d56b4-141">Конфигурация (воспользуйтесь **одним** из перечисленных ниже подходов):</span><span class="sxs-lookup"><span data-stu-id="d56b4-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="d56b4-142">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d56b4-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="d56b4-143">раздел **Конфигурация** > **Параметры приложения** для службы приложений на портале Azure (**Имя**: `Azure:SignalR:ServerStickyMode`, **Значение**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="d56b4-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="d56b4-144">Создайте профиль публикации приложений Azure Apps в Visual Studio для серверного приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="d56b4-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="d56b4-145">Добавьте в профиль зависимость **службы Azure SignalR** .</span><span class="sxs-lookup"><span data-stu-id="d56b4-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="d56b4-146">Если подписка Azure не имеет уже существующего экземпляра службы Azure SignalR для назначения приложению, выберите **Создайте новый экземпляр службы Azure SignalR** для предоставления нового экземпляра службы.</span><span class="sxs-lookup"><span data-stu-id="d56b4-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="d56b4-147">Опубликуйте приложение в Azure.</span><span class="sxs-lookup"><span data-stu-id="d56b4-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="d56b4-148">IIS</span><span class="sxs-lookup"><span data-stu-id="d56b4-148">IIS</span></span>

<span data-ttu-id="d56b4-149">При использовании IIS включите:</span><span class="sxs-lookup"><span data-stu-id="d56b4-149">When using IIS, enable:</span></span>

* <span data-ttu-id="d56b4-150">[WebSockets в IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="d56b4-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="d56b4-151">[Прикрепленные сеансы с маршрутизацией запросов приложений](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="d56b4-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="d56b4-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="d56b4-152">Kubernetes</span></span>

<span data-ttu-id="d56b4-153">Создайте определение входящего трафика со следующими [заметками Kubernetes для прикрепленных сеансов](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="d56b4-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="d56b4-154">Linux с Nginx</span><span class="sxs-lookup"><span data-stu-id="d56b4-154">Linux with Nginx</span></span>

<span data-ttu-id="d56b4-155">Для правильной работы WebSockets SignalR убедитесь, что для заголовков `Upgrade` и `Connection` прокси-сервера заданы следующие значения и `$connection_upgrade` сопоставлен с одним из следующих элементов:</span><span class="sxs-lookup"><span data-stu-id="d56b4-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="d56b4-156">Значение заголовка обновления по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d56b4-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="d56b4-157">`close`, если заголовок обновления отсутствует или пуст.</span><span class="sxs-lookup"><span data-stu-id="d56b4-157">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="d56b4-158">Дополнительные сведения см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="d56b4-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="d56b4-159">NGINX как прокси-сервер WebSocket</span><span class="sxs-lookup"><span data-stu-id="d56b4-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="d56b4-160">Использование прокси-сервера для WebSocket</span><span class="sxs-lookup"><span data-stu-id="d56b4-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="d56b4-161">Linux c Apache</span><span class="sxs-lookup"><span data-stu-id="d56b4-161">Linux with Apache</span></span>

<span data-ttu-id="d56b4-162">Чтобы разместить приложение Blazor на основе Apache в Linux, настройте `ProxyPass` для трафика HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d56b4-162">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="d56b4-163">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="d56b4-163">In the following example:</span></span>

* <span data-ttu-id="d56b4-164">Сервер Kestrel запущен на ведущем компьютере.</span><span class="sxs-lookup"><span data-stu-id="d56b4-164">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="d56b4-165">Приложение прослушивает трафик на порту 5000.</span><span class="sxs-lookup"><span data-stu-id="d56b4-165">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="d56b4-166">Включите следующие модули:</span><span class="sxs-lookup"><span data-stu-id="d56b4-166">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="d56b4-167">Проверьте консоль браузера на наличие ошибок WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d56b4-167">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="d56b4-168">Примеры ошибок приведены далее.</span><span class="sxs-lookup"><span data-stu-id="d56b4-168">Example errors:</span></span>

* <span data-ttu-id="d56b4-169">Firefox не может установить подключение к серверу по адресу ws://the-domain-name.tld/_blazor?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="d56b4-169">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="d56b4-170">Ошибка: Не удалось запустить транспорт "WebSockets": Ошибка: произошла ошибка транспорта.</span><span class="sxs-lookup"><span data-stu-id="d56b4-170">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="d56b4-171">Ошибка: не удалось запустить транспорт "LongPolling". TypeError: этот транспорт не определен.</span><span class="sxs-lookup"><span data-stu-id="d56b4-171">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="d56b4-172">Ошибка: не удается подключиться к серверу с помощью любого из доступных транспортов.</span><span class="sxs-lookup"><span data-stu-id="d56b4-172">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="d56b4-173">Сбой WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d56b4-173">WebSockets failed</span></span>
* <span data-ttu-id="d56b4-174">Ошибка: невозможно отправить данные, если подключение не находится в состоянии "Подключено".</span><span class="sxs-lookup"><span data-stu-id="d56b4-174">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="d56b4-175">Дополнительные сведения см. в [документации по Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="d56b4-175">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="d56b4-176">Измерение задержки сети</span><span class="sxs-lookup"><span data-stu-id="d56b4-176">Measure network latency</span></span>

<span data-ttu-id="d56b4-177">Для оценки задержки сети можно использовать [средства взаимодействия JavaScript](xref:blazor/call-javascript-from-dotnet), как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="d56b4-177">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="d56b4-178">Для удобной работы с пользовательским интерфейсом мы рекомендуем обеспечить стабильную задержку не более 250 мс.</span><span class="sxs-lookup"><span data-stu-id="d56b4-178">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
