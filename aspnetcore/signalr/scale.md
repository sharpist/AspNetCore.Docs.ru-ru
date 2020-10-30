---
title: 'ASP.NET Core :::no-loc(SignalR)::: производственного размещения и масштабирования'
author: bradygaster
description: 'Узнайте, как избежать проблем с производительностью и масштабированием в приложениях, использующих ASP.NET Core :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/scale
ms.openlocfilehash: d3e9cd23a55702bcf9b002dcce556428683afeca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052777"
---
# <a name="aspnet-core-no-locsignalr-hosting-and-scaling"></a><span data-ttu-id="70745-103">ASP.NET Core :::no-loc(SignalR)::: размещение и масштабирование</span><span class="sxs-lookup"><span data-stu-id="70745-103">ASP.NET Core :::no-loc(SignalR)::: hosting and scaling</span></span>

<span data-ttu-id="70745-104">[Эндрю Стантон-медперсонала](https://twitter.com/anurse), [Брейди Гастер](https://twitter.com/bradygaster)и [Tom Dykstra)](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="70745-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="70745-105">В этой статье описываются вопросы размещения и масштабирования для приложений с высоким уровнем трафика, использующих ASP.NET Core :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="70745-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core :::no-loc(SignalR):::.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="70745-106">Закрепленные сеансы</span><span class="sxs-lookup"><span data-stu-id="70745-106">Sticky Sessions</span></span>

<span data-ttu-id="70745-107">:::no-loc(SignalR)::: требует, чтобы все HTTP-запросы для определенного соединения обрабатывались одним и тем же процессом сервера.</span><span class="sxs-lookup"><span data-stu-id="70745-107">:::no-loc(SignalR)::: requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="70745-108">Если :::no-loc(SignalR)::: работает на ферме серверов (несколько серверов), необходимо использовать "прикрепленные сеансы".</span><span class="sxs-lookup"><span data-stu-id="70745-108">When :::no-loc(SignalR)::: is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="70745-109">«Прикрепленные сеансы» также называют сходством сеансов некоторыми подсистемами балансировки нагрузки.</span><span class="sxs-lookup"><span data-stu-id="70745-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="70745-110">Служба приложений Azure использует [маршрутизацию запросов приложений](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) для маршрутизации запросов.</span><span class="sxs-lookup"><span data-stu-id="70745-110">Azure App Service uses [Application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="70745-111">Включение параметра "Настройка сходства" в службе приложений Azure включит "закрепленные сеансы".</span><span class="sxs-lookup"><span data-stu-id="70745-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="70745-112">Существуют следующие ситуации, в которых не требуется закрепить сеансы.</span><span class="sxs-lookup"><span data-stu-id="70745-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="70745-113">При размещении на одном сервере в одном процессе.</span><span class="sxs-lookup"><span data-stu-id="70745-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="70745-114">При использовании службы Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="70745-114">When using the Azure :::no-loc(SignalR)::: Service.</span></span>
1. <span data-ttu-id="70745-115">Если все клиенты настроены на использование **только** WebSockets, **а** [параметр скипнеготиатион](xref:signalr/configuration#configure-additional-options) включен в конфигурации клиента.</span><span class="sxs-lookup"><span data-stu-id="70745-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="70745-116">Во всех остальных случаях (включая использование объединительной платы Redis) серверная среда должна быть настроена для работы с закрепленными сеансами.</span><span class="sxs-lookup"><span data-stu-id="70745-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="70745-117">Рекомендации по настройке службы приложений Azure для :::no-loc(SignalR)::: см. в разделе <xref:signalr/publish-to-azure-web-app> .</span><span class="sxs-lookup"><span data-stu-id="70745-117">For guidance on configuring Azure App Service for :::no-loc(SignalR):::, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="70745-118">Ресурсы TCP-подключения</span><span class="sxs-lookup"><span data-stu-id="70745-118">TCP connection resources</span></span>

<span data-ttu-id="70745-119">Количество одновременных TCP-подключений, которое может поддерживаться веб-сервером, ограничено.</span><span class="sxs-lookup"><span data-stu-id="70745-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="70745-120">Стандартные клиенты HTTP используют *временные* подключения.</span><span class="sxs-lookup"><span data-stu-id="70745-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="70745-121">Эти подключения могут быть закрыты, когда клиент переходит в состояние простоя и снова открывается позже.</span><span class="sxs-lookup"><span data-stu-id="70745-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="70745-122">С другой стороны, :::no-loc(SignalR)::: подключение является *постоянным* .</span><span class="sxs-lookup"><span data-stu-id="70745-122">On the other hand, a :::no-loc(SignalR)::: connection is *persistent* .</span></span> <span data-ttu-id="70745-123">:::no-loc(SignalR)::: соединения остаются открытыми, даже когда клиент переходит в состояние бездействия.</span><span class="sxs-lookup"><span data-stu-id="70745-123">:::no-loc(SignalR)::: connections stay open even when the client goes idle.</span></span> <span data-ttu-id="70745-124">В приложении с большим трафиком, которое обслуживает много клиентов, эти постоянные подключения могут привести к тому, что серверы достигли максимального числа подключений.</span><span class="sxs-lookup"><span data-stu-id="70745-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="70745-125">Постоянные подключения также потребляют некоторую дополнительную память для наблюдения за каждым подключением.</span><span class="sxs-lookup"><span data-stu-id="70745-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="70745-126">Интенсивное использование ресурсов, связанных с подключением, :::no-loc(SignalR)::: может повлиять на другие веб-приложения, размещенные на том же сервере.</span><span class="sxs-lookup"><span data-stu-id="70745-126">The heavy use of connection-related resources by :::no-loc(SignalR)::: can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="70745-127">Когда :::no-loc(SignalR)::: открывает и сохраняет последние доступные подключения TCP, другие веб-приложения на том же сервере также будут иметь недоступные для них подключения.</span><span class="sxs-lookup"><span data-stu-id="70745-127">When :::no-loc(SignalR)::: opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="70745-128">Если на сервере не хватает подключений, вы увидите случайные ошибки сокета и ошибки сброса соединения.</span><span class="sxs-lookup"><span data-stu-id="70745-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="70745-129">Пример:</span><span class="sxs-lookup"><span data-stu-id="70745-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="70745-130">Чтобы :::no-loc(SignalR)::: использование ресурсов не вызывало ошибок в других веб-приложениях, запустите :::no-loc(SignalR)::: на разных серверах другие веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="70745-130">To keep :::no-loc(SignalR)::: resource usage from causing errors in other web apps, run :::no-loc(SignalR)::: on different servers than your other web apps.</span></span>

<span data-ttu-id="70745-131">Чтобы :::no-loc(SignalR)::: использование ресурсов не вызывало ошибок в :::no-loc(SignalR)::: приложении, необходимо выполнить масштабирование, чтобы ограничить число подключений, которое должен выполнять сервер.</span><span class="sxs-lookup"><span data-stu-id="70745-131">To keep :::no-loc(SignalR)::: resource usage from causing errors in a :::no-loc(SignalR)::: app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="70745-132">Горизонтальное увеличение масштаба</span><span class="sxs-lookup"><span data-stu-id="70745-132">Scale out</span></span>

<span data-ttu-id="70745-133">Приложение, использующее, :::no-loc(SignalR)::: должно отследить все его подключения, что создает проблемы для фермы серверов.</span><span class="sxs-lookup"><span data-stu-id="70745-133">An app that uses :::no-loc(SignalR)::: needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="70745-134">Добавьте сервер, и он получает новые подключения, о которых не известны другие серверы.</span><span class="sxs-lookup"><span data-stu-id="70745-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="70745-135">Например, :::no-loc(SignalR)::: на каждом сервере на следующей схеме не знаются соединения на других серверах.</span><span class="sxs-lookup"><span data-stu-id="70745-135">For example, :::no-loc(SignalR)::: on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="70745-136">Когда :::no-loc(SignalR)::: на одном из серверов требуется отправить сообщение всем клиентам, оно передается только тем клиентам, которые подключены к этому серверу.</span><span class="sxs-lookup"><span data-stu-id="70745-136">When :::no-loc(SignalR)::: on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![Масштабирование::: No-Loc (SignalR)::: без объединительной платы](scale/_static/scale-no-backplane.png)

<span data-ttu-id="70745-138">Варианты решения этой проблемы: [ :::no-loc(SignalR)::: служба Azure](#azure-signalr-service) и [Redisная Объединительная плата](#redis-backplane).</span><span class="sxs-lookup"><span data-stu-id="70745-138">The options for solving this problem are the [Azure :::no-loc(SignalR)::: Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-no-locsignalr-service"></a><span data-ttu-id="70745-139">Служба Azure :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="70745-139">Azure :::no-loc(SignalR)::: Service</span></span>

<span data-ttu-id="70745-140">Служба Azure :::no-loc(SignalR)::: — это прокси-сервер, а не Объединительная плата.</span><span class="sxs-lookup"><span data-stu-id="70745-140">The Azure :::no-loc(SignalR)::: Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="70745-141">Каждый раз, когда клиент инициирует подключение к серверу, клиент перенаправляется для подключения к службе.</span><span class="sxs-lookup"><span data-stu-id="70745-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="70745-142">Этот процесс показан на следующей схеме:</span><span class="sxs-lookup"><span data-stu-id="70745-142">That process is illustrated in the following diagram:</span></span>

![Установление соединения с Azure::: No-Loc (SignalR)::: Service](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="70745-144">В результате служба управляет всеми клиентскими подключениями, в то время как каждому серверу требуется лишь небольшое постоянное число подключений к службе, как показано на следующей схеме:</span><span class="sxs-lookup"><span data-stu-id="70745-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![Клиенты, подключенные к службе, серверы, подключенные к службе](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="70745-146">Этот подход к горизонтальному масштабированию имеет несколько преимуществ по сравнению с альтернативой объединительной платы Redis:</span><span class="sxs-lookup"><span data-stu-id="70745-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="70745-147">Прикрепленные сеансы, также известные как [сходство клиентов](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), не являются обязательными, так как клиенты сразу же перенаправляются в :::no-loc(SignalR)::: службу Azure при подключении.</span><span class="sxs-lookup"><span data-stu-id="70745-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure :::no-loc(SignalR)::: Service when they connect.</span></span>
* <span data-ttu-id="70745-148">:::no-loc(SignalR):::Приложение может масштабироваться в зависимости от числа отправленных сообщений, а :::no-loc(SignalR)::: служба Azure масштабируется для выполнения любого числа подключений.</span><span class="sxs-lookup"><span data-stu-id="70745-148">A :::no-loc(SignalR)::: app can scale out based on the number of messages sent, while the Azure :::no-loc(SignalR)::: Service scales to handle any number of connections.</span></span> <span data-ttu-id="70745-149">Например, могут быть тысячи клиентов, но если отправлено всего несколько сообщений в секунду, :::no-loc(SignalR)::: приложению не придется масштабироваться на несколько серверов, чтобы обрабатывать сами подключения.</span><span class="sxs-lookup"><span data-stu-id="70745-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the :::no-loc(SignalR)::: app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="70745-150">:::no-loc(SignalR):::Приложение не будет использовать значительно больше ресурсов для подключения, чем веб-приложение :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="70745-150">A :::no-loc(SignalR)::: app won't use significantly more connection resources than a web app without :::no-loc(SignalR):::.</span></span>

<span data-ttu-id="70745-151">По этим причинам мы рекомендуем использовать службу Azure :::no-loc(SignalR)::: для всех ASP.NET Core :::no-loc(SignalR)::: приложений, размещенных в Azure, включая службу приложений, виртуальные машины и контейнеры.</span><span class="sxs-lookup"><span data-stu-id="70745-151">For these reasons, we recommend the Azure :::no-loc(SignalR)::: Service for all ASP.NET Core :::no-loc(SignalR)::: apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="70745-152">Дополнительные сведения см. в [ :::no-loc(SignalR)::: документации по службе Azure](/azure/azure-signalr/signalr-overview).</span><span class="sxs-lookup"><span data-stu-id="70745-152">For more information see the [Azure :::no-loc(SignalR)::: Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="70745-153">Канал обмена Redis</span><span class="sxs-lookup"><span data-stu-id="70745-153">Redis backplane</span></span>

<span data-ttu-id="70745-154">[Redis](https://redis.io/) — это хранилище "ключ — значение" в памяти, которое поддерживает систему обмена сообщениями с моделью публикации и подписки.</span><span class="sxs-lookup"><span data-stu-id="70745-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="70745-155">:::no-loc(SignalR):::Для пересылки сообщений на другие серверы в объединительной плате Redis используется функция Pub/подкаталогов.</span><span class="sxs-lookup"><span data-stu-id="70745-155">The :::no-loc(SignalR)::: Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="70745-156">Когда клиент устанавливает соединение, сведения о соединении передаются в объединительную плату.</span><span class="sxs-lookup"><span data-stu-id="70745-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="70745-157">Когда сервер хочет отправить сообщение всем клиентам, он отправляется в объединительную плату.</span><span class="sxs-lookup"><span data-stu-id="70745-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="70745-158">Объединительная плата знает все подключенные клиенты и серверы, на которых они находятся.</span><span class="sxs-lookup"><span data-stu-id="70745-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="70745-159">Он отправляет сообщение всем клиентам через соответствующие серверы.</span><span class="sxs-lookup"><span data-stu-id="70745-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="70745-160">Этот процесс показан на следующей схеме:</span><span class="sxs-lookup"><span data-stu-id="70745-160">This process is illustrated in the following diagram:</span></span>

![Redis Объединительная часть, сообщение, отправленное с одного сервера на все клиенты](scale/_static/redis-backplane.png)

<span data-ttu-id="70745-162">Объединительная плата Redis — это рекомендуемый подход к горизонтальному масштабированию для приложений, размещенных в собственной инфраструктуре.</span><span class="sxs-lookup"><span data-stu-id="70745-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="70745-163">Если между центром обработки данных и центром обработки данных Azure возникает значительная задержка подключения, :::no-loc(SignalR)::: служба Azure может оказаться непрактичной для локальных приложений с низкой задержкой или требованиями высокой пропускной способности.</span><span class="sxs-lookup"><span data-stu-id="70745-163">If there is significant connection latency between your data center and an Azure data center, Azure :::no-loc(SignalR)::: Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="70745-164">:::no-loc(SignalR):::Преимущества службы Azure, упомянутые ранее, являются недостатками объединительной платы Redis:</span><span class="sxs-lookup"><span data-stu-id="70745-164">The Azure :::no-loc(SignalR)::: Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="70745-165">Прикрепленные сеансы, также известные как [сходство клиентов](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), являются обязательными, за исключением случаев, когда выполняются **оба** следующих условия.</span><span class="sxs-lookup"><span data-stu-id="70745-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="70745-166">Все клиенты настроены для использования **только** WebSockets.</span><span class="sxs-lookup"><span data-stu-id="70745-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="70745-167">[Параметр скипнеготиатион](xref:signalr/configuration#configure-additional-options) включен в конфигурации клиента.</span><span class="sxs-lookup"><span data-stu-id="70745-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="70745-168">После инициации подключения на сервере соединение должно остаться на этом сервере.</span><span class="sxs-lookup"><span data-stu-id="70745-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="70745-169">:::no-loc(SignalR):::Приложение должно масштабироваться на основе числа клиентов, даже если отправляется несколько сообщений.</span><span class="sxs-lookup"><span data-stu-id="70745-169">A :::no-loc(SignalR)::: app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="70745-170">:::no-loc(SignalR):::Приложение использует значительно больше ресурсов для подключения, чем веб-приложение, без :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="70745-170">A :::no-loc(SignalR)::: app uses significantly more connection resources than a web app without :::no-loc(SignalR):::.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="70745-171">Ограничения IIS в клиентской ОС Windows</span><span class="sxs-lookup"><span data-stu-id="70745-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="70745-172">Windows 10 и Windows 8. x являются клиентскими операционными системами.</span><span class="sxs-lookup"><span data-stu-id="70745-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="70745-173">Службы IIS в клиентских операционных системах имеют ограничение в 10 одновременных подключений.</span><span class="sxs-lookup"><span data-stu-id="70745-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="70745-174">:::no-loc(SignalR):::подключения:</span><span class="sxs-lookup"><span data-stu-id="70745-174">:::no-loc(SignalR):::'s connections are:</span></span>

* <span data-ttu-id="70745-175">Временное и часто переустановленное.</span><span class="sxs-lookup"><span data-stu-id="70745-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="70745-176">**Не** удаляется сразу, когда больше не используется.</span><span class="sxs-lookup"><span data-stu-id="70745-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="70745-177">Предыдущие условия, вероятно, пристигают 10 ограничений на подключение к клиентской ОС.</span><span class="sxs-lookup"><span data-stu-id="70745-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="70745-178">При использовании клиентской ОС для разработки рекомендуется:</span><span class="sxs-lookup"><span data-stu-id="70745-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="70745-179">Избегайте IIS.</span><span class="sxs-lookup"><span data-stu-id="70745-179">Avoid IIS.</span></span>
* <span data-ttu-id="70745-180">Используйте Kestrel или IIS Express в качестве целевых объектов развертывания.</span><span class="sxs-lookup"><span data-stu-id="70745-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="70745-181">Linux с Nginx</span><span class="sxs-lookup"><span data-stu-id="70745-181">Linux with Nginx</span></span>

<span data-ttu-id="70745-182">Ниже приведены минимальные необходимые параметры для включения WebSockets, Серверсентевентс и Лонгполлинг для :::no-loc(SignalR)::: :</span><span class="sxs-lookup"><span data-stu-id="70745-182">The following contains the minimum required settings to enable WebSockets, ServerSentEvents, and LongPolling for :::no-loc(SignalR)::::</span></span>

```nginx
http {
  map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

  server {
    listen 80;
    server_name example.com *.example.com;

    # Configure the :::no-loc(SignalR)::: Endpoint
    location /hubroute {
      # App server url
      proxy_pass http://localhost:5000;

      # Configuration for WebSockets
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_cache off;

      # Configuration for ServerSentEvents
      proxy_buffering off;

      # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
      proxy_read_timeout 100s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

<span data-ttu-id="70745-183">При использовании нескольких внутренних серверов необходимо добавить прикрепленные сеансы, чтобы предотвратить :::no-loc(SignalR)::: Переключение серверов при подключении.</span><span class="sxs-lookup"><span data-stu-id="70745-183">When multiple backend servers are used, sticky sessions must be added to prevent :::no-loc(SignalR)::: connections from switching servers when connecting.</span></span> <span data-ttu-id="70745-184">Существует несколько способов добавления прикрепленных сеансов в nginx.</span><span class="sxs-lookup"><span data-stu-id="70745-184">There are multiple ways to add sticky sessions in Nginx.</span></span> <span data-ttu-id="70745-185">Ниже показаны два подхода в зависимости от доступных возможностей.</span><span class="sxs-lookup"><span data-stu-id="70745-185">Two approaches are shown below depending on what you have available.</span></span>

<span data-ttu-id="70745-186">В дополнение к предыдущей конфигурации добавляется следующее.</span><span class="sxs-lookup"><span data-stu-id="70745-186">The following is added in addition to the previous configuration.</span></span> <span data-ttu-id="70745-187">В следующих примерах `backend` — это имя группы серверов.</span><span class="sxs-lookup"><span data-stu-id="70745-187">In the following examples, `backend` is the name of the group of servers.</span></span>

<span data-ttu-id="70745-188">С [открытым исходным кодом nginx](https://nginx.org/en/)используйте `ip_hash` для маршрутизации подключений к серверу на основе IP-адреса клиента:</span><span class="sxs-lookup"><span data-stu-id="70745-188">With [Nginx Open Source](https://nginx.org/en/), use `ip_hash` to route connections to a server based on the client's IP address:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    ip_hash;
  }
}
```

<span data-ttu-id="70745-189">С помощью [nginx Plus](https://www.nginx.com/products/nginx)используйте, `sticky` чтобы добавить :::no-loc(cookie)::: запрос в запросы и закрепить запросы пользователя на сервере:</span><span class="sxs-lookup"><span data-stu-id="70745-189">With [Nginx Plus](https://www.nginx.com/products/nginx), use `sticky` to add a :::no-loc(cookie)::: to requests and pin the user's requests to a server:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    sticky :::no-loc(cookie)::: srv_id expires=max domain=.example.com path=/ httponly;
  }
}
```

<span data-ttu-id="70745-190">Наконец, измените `proxy_pass http://localhost:5000` в `server` разделе на `proxy_pass http://backend` .</span><span class="sxs-lookup"><span data-stu-id="70745-190">Finally, change `proxy_pass http://localhost:5000` in the `server` section to `proxy_pass http://backend`.</span></span>

<span data-ttu-id="70745-191">Дополнительные сведения о WebSockets через nginx см. в разделе [nginx как прокси-сервер](https://www.nginx.com/blog/websocket-nginx).</span><span class="sxs-lookup"><span data-stu-id="70745-191">For more information on WebSockets over Nginx, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx).</span></span>

<span data-ttu-id="70745-192">Дополнительные сведения о балансировке нагрузки и закрепленных сеансах см. в разделе [nginx Load балансировка нагрузки](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).</span><span class="sxs-lookup"><span data-stu-id="70745-192">For more information on load balancing and sticky sessions, see [NGINX load balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).</span></span>

<span data-ttu-id="70745-193">Дополнительные сведения о ASP.NET Core с nginx см. в следующей статье:</span><span class="sxs-lookup"><span data-stu-id="70745-193">For more information about ASP.NET Core with Nginx see the following article:</span></span>
* <xref:host-and-deploy/linux-nginx>

## <a name="third-party-no-locsignalr-backplane-providers"></a><span data-ttu-id="70745-194">Сторонние :::no-loc(SignalR)::: поставщики объединительной платы</span><span class="sxs-lookup"><span data-stu-id="70745-194">Third-party :::no-loc(SignalR)::: backplane providers</span></span>

* [<span data-ttu-id="70745-195">NCache</span><span class="sxs-lookup"><span data-stu-id="70745-195">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="70745-196">[Orleans](https://github.com/OrleansContrib/:::no-loc(SignalR):::.Orleans)</span><span class="sxs-lookup"><span data-stu-id="70745-196">[Orleans](https://github.com/OrleansContrib/:::no-loc(SignalR):::.Orleans)</span></span>

## <a name="next-steps"></a><span data-ttu-id="70745-197">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="70745-197">Next steps</span></span>

<span data-ttu-id="70745-198">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="70745-198">For more information, see the following resources:</span></span>

* [<span data-ttu-id="70745-199">:::no-loc(SignalR):::Документация по службе Azure</span><span class="sxs-lookup"><span data-stu-id="70745-199">Azure :::no-loc(SignalR)::: Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="70745-200">Настройка объединительной платы Redis</span><span class="sxs-lookup"><span data-stu-id="70745-200">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
