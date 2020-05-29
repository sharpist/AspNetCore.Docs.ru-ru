---
<span data-ttu-id="c302a-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-102">'Blazor'</span></span>
- <span data-ttu-id="c302a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-103">'Identity'</span></span>
- <span data-ttu-id="c302a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-105">'Razor'</span></span>
- <span data-ttu-id="c302a-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="c302a-107">Размещение ASP.NET Core в Windows со службами IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-107">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c302a-108">Руководство по публикации приложения ASP.NET Core на сервере IIS см. по этой ссылке: <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="c302a-108">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="c302a-109">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="c302a-109">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="c302a-110">Поддерживаемые операционные системы</span><span class="sxs-lookup"><span data-stu-id="c302a-110">Supported operating systems</span></span>

<span data-ttu-id="c302a-111">Поддерживаются следующие операционные системы:</span><span class="sxs-lookup"><span data-stu-id="c302a-111">The following operating systems are supported:</span></span>

* <span data-ttu-id="c302a-112">Windows 7 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="c302a-112">Windows 7 or later</span></span>
* <span data-ttu-id="c302a-113">Windows Server 2012 R2 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="c302a-113">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="c302a-114">[Сервер HTTP.sys](xref:fundamentals/servers/httpsys) (ранее назывался WebListener) не работает в конфигурации обратного прокси-сервера со службами IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="c302a-115">Используйте [сервер Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="c302a-115">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="c302a-116">Сведения о размещении в Azure см. в статье <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="c302a-116">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="c302a-117">Рекомендации по устранению неполадок см. в статье <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="c302a-117">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="c302a-118">Поддерживаемые платформы</span><span class="sxs-lookup"><span data-stu-id="c302a-118">Supported platforms</span></span>

<span data-ttu-id="c302a-119">Поддерживаются приложения, опубликованные для развертывания в 32-разрядных (x86) или 64-разрядных (x64) системах.</span><span class="sxs-lookup"><span data-stu-id="c302a-119">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="c302a-120">Развертывайте 32-разрядную версию (x86) приложения с использованием 32-разрядного пакета SDK для .NET Core, кроме следующих случаев:</span><span class="sxs-lookup"><span data-stu-id="c302a-120">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="c302a-121">Приложению требуется более широкое адресное пространство виртуальной памяти, доступное в 64-разрядной версии приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-121">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="c302a-122">Приложению требуется стек IIS большего размера.</span><span class="sxs-lookup"><span data-stu-id="c302a-122">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="c302a-123">В коде присутствуют зависимости 64-разрядной версии.</span><span class="sxs-lookup"><span data-stu-id="c302a-123">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="c302a-124">Для приложений, опубликованных для 32-разрядной архитектуры (x86), необходимо включить поддержку этой архитектуры для пулов приложений IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-124">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="c302a-125">Дополнительные сведения см. в разделе [Создание сайта IIS](#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="c302a-125">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="c302a-126">Используйте 64-разрядный (x64) пакет SDK для .NET Core для публикации 64-разрядной версии приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-126">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="c302a-127">Для этого в системе узла должна присутствовать 64-разрядная версия среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="c302a-127">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="c302a-128">Модели размещения</span><span class="sxs-lookup"><span data-stu-id="c302a-128">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="c302a-129">Модель внутрипроцессного размещения</span><span class="sxs-lookup"><span data-stu-id="c302a-129">In-process hosting model</span></span>

<span data-ttu-id="c302a-130">При внутрипроцессном размещении приложение ASP.NET Core выполняется в том же процессе, что и рабочий процесс IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="c302a-131">При этом повышается производительность по сравнению с внепроцессным размещением, так как запросы не передаются через адаптер замыкания на себя (сетевой интерфейс, который возвращает исходящий сетевой трафик на тот же компьютер).</span><span class="sxs-lookup"><span data-stu-id="c302a-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="c302a-132">IIS обрабатывает управление процессом с помощью [службы активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="c302a-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c302a-133">[Модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="c302a-133">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="c302a-134">Инициализирует приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-134">Performs app initialization.</span></span>
  * <span data-ttu-id="c302a-135">Загружает [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="c302a-135">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="c302a-136">Вызывает `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="c302a-136">Calls `Program.Main`.</span></span>
* <span data-ttu-id="c302a-137">Управляет жизненным циклом собственного запроса IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-137">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="c302a-138">На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением, размещенным внутри процесса.</span><span class="sxs-lookup"><span data-stu-id="c302a-138">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Модуль ASP.NET Core в сценарии внутрипроцессного размещения](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="c302a-140">Запрос поступает из Интернета в драйвер HTTP.sys в режиме ядра.</span><span class="sxs-lookup"><span data-stu-id="c302a-140">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="c302a-141">Драйвер направляет собственный запрос к IIS на настроенный порт веб-сайта — обычно 80 (HTTP) или 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c302a-141">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="c302a-142">Модуль ASP.NET Core получает собственный запрос и передает его на HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="c302a-142">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="c302a-143">HTTP-сервер IIS — это реализация внутрипроцессного сервера для IIS, в которой запрос преобразовывается из собственной формы в управляемую.</span><span class="sxs-lookup"><span data-stu-id="c302a-143">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="c302a-144">После того как HTTP-сервер IIS обработает запрос:</span><span class="sxs-lookup"><span data-stu-id="c302a-144">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="c302a-145">Запрос отправляется в конвейер ПО промежуточного слоя ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-145">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="c302a-146">Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-146">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="c302a-147">Ответ приложения передается обратно в службы IIS через HTTP-сервер IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-147">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="c302a-148">IIS отправляет ответ клиенту, который инициировал запрос.</span><span class="sxs-lookup"><span data-stu-id="c302a-148">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="c302a-149">Внутрипроцессное размещение подходит для существующих приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-149">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="c302a-150">В веб-шаблонах ASP.NET Core используется модель внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="c302a-150">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="c302a-151">`CreateDefaultBuilder` добавляет экземпляр <xref:Microsoft.AspNetCore.Hosting.Server.IServer>. При этом вызывается метод <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> для загрузки [CoreCLR](/dotnet/standard/glossary#coreclr) и размещения приложения внутри рабочего процесса IIS (*w3wp.exe* или *iisexpress.exe*).</span><span class="sxs-lookup"><span data-stu-id="c302a-151">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="c302a-152">Тесты производительности показывают, что размещение приложения .NET Core внутри процесса позволяет обрабатывать значительно больше запросов, чем при размещении приложения вне процесса с перенаправлением запросов к [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="c302a-152">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="c302a-153">Приложения, опубликованные в виде одного исполняемого файла, не могут быть загружены по модели внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="c302a-153">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="c302a-154">Модель размещения вне процесса</span><span class="sxs-lookup"><span data-stu-id="c302a-154">Out-of-process hosting model</span></span>

<span data-ttu-id="c302a-155">Так как приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, модуль ASP.NET Core обрабатывает управление процессами.</span><span class="sxs-lookup"><span data-stu-id="c302a-155">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="c302a-156">Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="c302a-156">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="c302a-157">Это, по сути, совпадает с поведением приложений, выполняемых внутрипроцессно и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="c302a-157">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c302a-158">На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением, размещенным вне процесса.</span><span class="sxs-lookup"><span data-stu-id="c302a-158">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Модуль ASP.NET Core в сценарии внепроцессного размещения](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="c302a-160">Запросы поступают из Интернета в драйвер HTTP.sys в режиме ядра.</span><span class="sxs-lookup"><span data-stu-id="c302a-160">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="c302a-161">Драйвер направляет запросы к службам IIS на настроенный порт веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="c302a-161">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="c302a-162">Обычно это порт 80 (HTTP) или порт 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c302a-162">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="c302a-163">Модуль перенаправляет запросы Kestrel на случайный порт для приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-163">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="c302a-164">В качестве случайного порта не могут использоваться порты 80 и 443.</span><span class="sxs-lookup"><span data-stu-id="c302a-164">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="c302a-165">Модуль ASP.NET Core задает порт с помощью переменной среды при запуске.</span><span class="sxs-lookup"><span data-stu-id="c302a-165">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="c302a-166">Расширение <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> настраивает сервер для прослушивания `http://localhost:{PORT}`.</span><span class="sxs-lookup"><span data-stu-id="c302a-166">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="c302a-167">Выполняются дополнительные проверки, и запросы не из модуля отклоняются.</span><span class="sxs-lookup"><span data-stu-id="c302a-167">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="c302a-168">Модуль не поддерживает переадресацию по HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c302a-168">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="c302a-169">Запросы переадресовываются по протоколу HTTP, даже если были получены IIS по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c302a-169">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="c302a-170">После того как Kestrel получает запрос из модуля, запрос пересылается в конвейер ПО промежуточного слоя ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-170">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="c302a-171">Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-171">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="c302a-172">ПО промежуточного слоя, добавленное интеграцией IIS, обновляет схему, удаленный IP-адрес и базовый путь для переадресации запроса в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c302a-172">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="c302a-173">Ответ приложения передается обратно в службу IIS, которая пересылает его HTTP-клиенту, инициировавшему запрос.</span><span class="sxs-lookup"><span data-stu-id="c302a-173">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="c302a-174">Инструкции по настройке модуля ASP.NET Core см. в статье <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c302a-174">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="c302a-175">Дополнительные сведения о размещении см. в разделе [Размещение в ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="c302a-175">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="c302a-176">Настройка приложения</span><span class="sxs-lookup"><span data-stu-id="c302a-176">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="c302a-177">Включение компонентов IISIntegration</span><span class="sxs-lookup"><span data-stu-id="c302a-177">Enable the IISIntegration components</span></span>

<span data-ttu-id="c302a-178">При создании узла в `CreateHostBuilder` (*Program.cs*) вызовите метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, чтобы включить интеграцию IIS:</span><span class="sxs-lookup"><span data-stu-id="c302a-178">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="c302a-179">Дополнительные сведения о методе `CreateDefaultBuilder` см. в разделе <xref:fundamentals/host/generic-host#default-builder-settings>.</span><span class="sxs-lookup"><span data-stu-id="c302a-179">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="c302a-180">Параметры служб IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-180">IIS options</span></span>

<span data-ttu-id="c302a-181">**Модель внутрипроцессного размещения**</span><span class="sxs-lookup"><span data-stu-id="c302a-181">**In-process hosting model**</span></span>

<span data-ttu-id="c302a-182">Чтобы настроить параметры сервера IIS, включите конфигурацию служб для <xref:Microsoft.AspNetCore.Builder.IISServerOptions> в <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="c302a-182">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="c302a-183">В следующем примере показано, как отключить AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="c302a-183">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="c302a-184">Параметр</span><span class="sxs-lookup"><span data-stu-id="c302a-184">Option</span></span>                         | <span data-ttu-id="c302a-185">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c302a-185">Default</span></span> | <span data-ttu-id="c302a-186">Параметр</span><span class="sxs-lookup"><span data-stu-id="c302a-186">Setting</span></span> |
| ---
<span data-ttu-id="c302a-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-188">'Blazor'</span></span>
- <span data-ttu-id="c302a-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-189">'Identity'</span></span>
- <span data-ttu-id="c302a-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-191">'Razor'</span></span>
- <span data-ttu-id="c302a-192">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-194">'Blazor'</span></span>
- <span data-ttu-id="c302a-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-195">'Identity'</span></span>
- <span data-ttu-id="c302a-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-197">'Razor'</span></span>
- <span data-ttu-id="c302a-198">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-200">'Blazor'</span></span>
- <span data-ttu-id="c302a-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-201">'Identity'</span></span>
- <span data-ttu-id="c302a-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-203">'Razor'</span></span>
- <span data-ttu-id="c302a-204">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-206">'Blazor'</span></span>
- <span data-ttu-id="c302a-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-207">'Identity'</span></span>
- <span data-ttu-id="c302a-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-209">'Razor'</span></span>
- <span data-ttu-id="c302a-210">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-212">'Blazor'</span></span>
- <span data-ttu-id="c302a-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-213">'Identity'</span></span>
- <span data-ttu-id="c302a-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-215">'Razor'</span></span>
- <span data-ttu-id="c302a-216">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-218">'Blazor'</span></span>
- <span data-ttu-id="c302a-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-219">'Identity'</span></span>
- <span data-ttu-id="c302a-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-221">'Razor'</span></span>
- <span data-ttu-id="c302a-222">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-224">'Blazor'</span></span>
- <span data-ttu-id="c302a-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-225">'Identity'</span></span>
- <span data-ttu-id="c302a-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-227">'Razor'</span></span>
- <span data-ttu-id="c302a-228">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-230">'Blazor'</span></span>
- <span data-ttu-id="c302a-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-231">'Identity'</span></span>
- <span data-ttu-id="c302a-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-233">'Razor'</span></span>
- <span data-ttu-id="c302a-234">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-236">'Blazor'</span></span>
- <span data-ttu-id="c302a-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-237">'Identity'</span></span>
- <span data-ttu-id="c302a-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-239">'Razor'</span></span>
- <span data-ttu-id="c302a-240">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-242">'Blazor'</span></span>
- <span data-ttu-id="c302a-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-243">'Identity'</span></span>
- <span data-ttu-id="c302a-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-245">'Razor'</span></span>
- <span data-ttu-id="c302a-246">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-248">'Blazor'</span></span>
- <span data-ttu-id="c302a-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-249">'Identity'</span></span>
- <span data-ttu-id="c302a-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-251">'Razor'</span></span>
- <span data-ttu-id="c302a-252">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-254">'Blazor'</span></span>
- <span data-ttu-id="c302a-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-255">'Identity'</span></span>
- <span data-ttu-id="c302a-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-257">'Razor'</span></span>
- <span data-ttu-id="c302a-258">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-260">'Blazor'</span></span>
- <span data-ttu-id="c302a-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-261">'Identity'</span></span>
- <span data-ttu-id="c302a-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-263">'Razor'</span></span>
- <span data-ttu-id="c302a-264">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-264">'SignalR' uid:</span></span> 

<span data-ttu-id="c302a-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-266">'Blazor'</span></span>
- <span data-ttu-id="c302a-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-267">'Identity'</span></span>
- <span data-ttu-id="c302a-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-269">'Razor'</span></span>
- <span data-ttu-id="c302a-270">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-270">'SignalR' uid:</span></span> 

<span data-ttu-id="c302a-271">---- | | `AutomaticAuthentication`      | `true`  | Если значение — `true`, сервер IIS задает свойство `HttpContext.User`, использующее [проверку подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-271">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c302a-272">Если значение — `false`, сервер только предоставляет идентификатор для `HttpContext.User` и отвечает на явные запросы защиты от `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="c302a-272">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="c302a-273">Для работы `AutomaticAuthentication` необходимо включить в службах IIS проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="c302a-273">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="c302a-274">Дополнительные сведения: [Проверка подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-274">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c302a-275">| | `AuthenticationDisplayName`    | `null`  | Задает отображаемое имя для пользователей на страницах входа.</span><span class="sxs-lookup"><span data-stu-id="c302a-275">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="c302a-276">| | `AllowSynchronousIO`           | `false` | Разрешены ли синхронные операции ввода-вывода для `HttpContext.Request` и `HttpContext.Response`.</span><span class="sxs-lookup"><span data-stu-id="c302a-276">| | `AllowSynchronousIO`           | `false` | Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> <span data-ttu-id="c302a-277">| | `MaxRequestBodySize`           | `30000000`  | Возвращает или задает максимальный размер текста запроса для `HttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="c302a-277">| | `MaxRequestBodySize`           | `30000000`  | Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="c302a-278">Обратите внимание, что сами службы IIS ограничены параметром `maxAllowedContentLength`, который обрабатывается перед тем, как `MaxRequestBodySize` задается в `IISServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="c302a-278">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="c302a-279">Изменение `MaxRequestBodySize` не влияет на `maxAllowedContentLength`.</span><span class="sxs-lookup"><span data-stu-id="c302a-279">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="c302a-280">Чтобы увеличить `maxAllowedContentLength`, добавьте запись в *web.config*, чтобы задать `maxAllowedContentLength` большее значение.</span><span class="sxs-lookup"><span data-stu-id="c302a-280">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="c302a-281">Дополнительные сведения см. в разделе [Конфигурация](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="c302a-281">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="c302a-282">**Модель размещения вне процесса**</span><span class="sxs-lookup"><span data-stu-id="c302a-282">**Out-of-process hosting model**</span></span>

<span data-ttu-id="c302a-283">Чтобы настроить параметры IIS, включите конфигурацию служб для <xref:Microsoft.AspNetCore.Builder.IISOptions> в <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="c302a-283">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="c302a-284">В следующем примере приложению запрещается заполнение `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="c302a-284">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="c302a-285">Параметр</span><span class="sxs-lookup"><span data-stu-id="c302a-285">Option</span></span>                         | <span data-ttu-id="c302a-286">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c302a-286">Default</span></span> | <span data-ttu-id="c302a-287">Параметр</span><span class="sxs-lookup"><span data-stu-id="c302a-287">Setting</span></span> |
| ---
<span data-ttu-id="c302a-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-289">'Blazor'</span></span>
- <span data-ttu-id="c302a-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-290">'Identity'</span></span>
- <span data-ttu-id="c302a-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-292">'Razor'</span></span>
- <span data-ttu-id="c302a-293">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-295">'Blazor'</span></span>
- <span data-ttu-id="c302a-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-296">'Identity'</span></span>
- <span data-ttu-id="c302a-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-298">'Razor'</span></span>
- <span data-ttu-id="c302a-299">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-301">'Blazor'</span></span>
- <span data-ttu-id="c302a-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-302">'Identity'</span></span>
- <span data-ttu-id="c302a-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-304">'Razor'</span></span>
- <span data-ttu-id="c302a-305">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-307">'Blazor'</span></span>
- <span data-ttu-id="c302a-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-308">'Identity'</span></span>
- <span data-ttu-id="c302a-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-310">'Razor'</span></span>
- <span data-ttu-id="c302a-311">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-313">'Blazor'</span></span>
- <span data-ttu-id="c302a-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-314">'Identity'</span></span>
- <span data-ttu-id="c302a-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-316">'Razor'</span></span>
- <span data-ttu-id="c302a-317">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-319">'Blazor'</span></span>
- <span data-ttu-id="c302a-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-320">'Identity'</span></span>
- <span data-ttu-id="c302a-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-322">'Razor'</span></span>
- <span data-ttu-id="c302a-323">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-325">'Blazor'</span></span>
- <span data-ttu-id="c302a-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-326">'Identity'</span></span>
- <span data-ttu-id="c302a-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-328">'Razor'</span></span>
- <span data-ttu-id="c302a-329">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-331">'Blazor'</span></span>
- <span data-ttu-id="c302a-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-332">'Identity'</span></span>
- <span data-ttu-id="c302a-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-334">'Razor'</span></span>
- <span data-ttu-id="c302a-335">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-337">'Blazor'</span></span>
- <span data-ttu-id="c302a-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-338">'Identity'</span></span>
- <span data-ttu-id="c302a-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-340">'Razor'</span></span>
- <span data-ttu-id="c302a-341">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-343">'Blazor'</span></span>
- <span data-ttu-id="c302a-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-344">'Identity'</span></span>
- <span data-ttu-id="c302a-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-346">'Razor'</span></span>
- <span data-ttu-id="c302a-347">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-349">'Blazor'</span></span>
- <span data-ttu-id="c302a-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-350">'Identity'</span></span>
- <span data-ttu-id="c302a-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-352">'Razor'</span></span>
- <span data-ttu-id="c302a-353">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-355">'Blazor'</span></span>
- <span data-ttu-id="c302a-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-356">'Identity'</span></span>
- <span data-ttu-id="c302a-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-358">'Razor'</span></span>
- <span data-ttu-id="c302a-359">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-361">'Blazor'</span></span>
- <span data-ttu-id="c302a-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-362">'Identity'</span></span>
- <span data-ttu-id="c302a-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-364">'Razor'</span></span>
- <span data-ttu-id="c302a-365">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-365">'SignalR' uid:</span></span> 

<span data-ttu-id="c302a-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-367">'Blazor'</span></span>
- <span data-ttu-id="c302a-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-368">'Identity'</span></span>
- <span data-ttu-id="c302a-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-370">'Razor'</span></span>
- <span data-ttu-id="c302a-371">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-371">'SignalR' uid:</span></span> 

<span data-ttu-id="c302a-372">---- | | `AutomaticAuthentication`      | `true`  | Если значение — `true`, [ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components) задает свойство `HttpContext.User`, которое прошло [проверку подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-372">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c302a-373">Если значение — `false`, ПО промежуточного слоя только предоставляет идентификатор для `HttpContext.User` и отвечает на явные запросы защиты от `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="c302a-373">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="c302a-374">Для работы `AutomaticAuthentication` необходимо включить в службах IIS проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="c302a-374">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="c302a-375">Дополнительные сведения см. в статье о [проверке подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-375">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="c302a-376">| | `AuthenticationDisplayName`    | `null`  | Задает отображаемое имя для пользователей на страницах входа.</span><span class="sxs-lookup"><span data-stu-id="c302a-376">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="c302a-377">| | `ForwardClientCertificate`     | `true`  | Если значение — `true` и если присутствует заголовок запроса `MS-ASPNETCORE-CLIENTCERT`, происходит заполнение `HttpContext.Connection.ClientCertificate`.</span><span class="sxs-lookup"><span data-stu-id="c302a-377">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c302a-378">Сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="c302a-378">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c302a-379">[ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components) и модуль ASP.NET Core настроены для пересылки:</span><span class="sxs-lookup"><span data-stu-id="c302a-379">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="c302a-380">схемы (HTTP/HTTPS);</span><span class="sxs-lookup"><span data-stu-id="c302a-380">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="c302a-381">удаленного IP-адреса, на котором возник запрос.</span><span class="sxs-lookup"><span data-stu-id="c302a-381">Remote IP address where the request originated.</span></span>

<span data-ttu-id="c302a-382">[ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components) настраивает ПО промежуточного слоя переадресации заголовков.</span><span class="sxs-lookup"><span data-stu-id="c302a-382">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="c302a-383">Для приложений, размещенных за дополнительными прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="c302a-383">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="c302a-384">Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="c302a-384">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="c302a-385">Файл web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-385">web.config file</span></span>

<span data-ttu-id="c302a-386">В файле *web.config* содержится конфигурация [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c302a-386">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="c302a-387">Создание, преобразование и публикация файла *web.config* обрабатываются целевым объектом MSBuild (`_TransformWebConfig`) при публикации проекта.</span><span class="sxs-lookup"><span data-stu-id="c302a-387">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="c302a-388">Этот целевой объект присутствует в целевых веб-пакетах SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="c302a-388">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="c302a-389">Пакет SDK задается в начале файла проекта:</span><span class="sxs-lookup"><span data-stu-id="c302a-389">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="c302a-390">Если в проекте нет файла *web.config*, он создается с соответствующими аргументами *processPath* и *arguments* для настройки модуля ASP.NET Core и переносится в [опубликованные выходные данные](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="c302a-390">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="c302a-391">Если в проекте есть файл *web.config*, он преобразуется с соответствующими аргументами *processPath* и *arguments* для настройки модуля ASP.NET Core и переносится в опубликованные выходные данные.</span><span class="sxs-lookup"><span data-stu-id="c302a-391">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="c302a-392">Преобразование не изменяет параметры конфигурации служб IIS, включенные в файл.</span><span class="sxs-lookup"><span data-stu-id="c302a-392">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="c302a-393">Файл *web.config* может содержать дополнительные параметры конфигурации IIS, управляющие активными модулями IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-393">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="c302a-394">Сведения о модулях IIS, которые могут обрабатывать запросы к приложениям ASP.NET Core, см. в статье [Модули IIS](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="c302a-394">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="c302a-395">Чтобы пакет SDK не преобразовывал файл *web.config*, добавьте в файл проекта свойство **\<IsTransformWebConfigDisabled>** :</span><span class="sxs-lookup"><span data-stu-id="c302a-395">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="c302a-396">Если пакет SDK не преобразует файл, аргументы *processPath* и *arguments* нужно задать вручную.</span><span class="sxs-lookup"><span data-stu-id="c302a-396">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="c302a-397">Для получения дополнительной информации см. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c302a-397">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="c302a-398">Расположение файла web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-398">web.config file location</span></span>

<span data-ttu-id="c302a-399">Для корректной настройки [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module) необходимо наличие файла *web.config* в [корневой папке содержимого](xref:fundamentals/index#content-root) развертываемого приложения (как правило, это основной путь приложения).</span><span class="sxs-lookup"><span data-stu-id="c302a-399">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="c302a-400">Это расположение соответствует физическому пути веб-сайта, указанному в службах IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-400">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="c302a-401">Файл *web.config* требуется в корне приложения, чтобы разрешить публикацию нескольких приложений с помощью веб-развертывания.</span><span class="sxs-lookup"><span data-stu-id="c302a-401">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="c302a-402">По физическому пути приложения находятся файлы с конфиденциальной информацией: *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (комментарии к XML-документации) и *\<assembly>.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="c302a-402">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="c302a-403">Когда файл *web.config* присутствует и сайт запускается нормально, службы IIS не обрабатывают запросы к этим файлам.</span><span class="sxs-lookup"><span data-stu-id="c302a-403">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="c302a-404">Если файл *web.config* отсутствует, неправильно именован или не может настроить нормальный запуск сайта, службы IIS могут свободно передавать содержимое этих конфиденциальных файлов.</span><span class="sxs-lookup"><span data-stu-id="c302a-404">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="c302a-405">**Файл *web.config* должен постоянно находиться в развертывании, у этого файла должно быть правильное имя, и файл должен быть в состоянии настроить нормальный запуск сайта. Никогда не удаляйте файл *web.config* из развертывания в рабочей среде.**</span><span class="sxs-lookup"><span data-stu-id="c302a-405">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="c302a-406">Преобразование web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-406">Transform web.config</span></span>

<span data-ttu-id="c302a-407">Если вам необходимо преобразовать *web.config* при публикации, обратитесь к разделу <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="c302a-407">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="c302a-408">Вам может потребоваться преобразовать *web.config* при публикации, чтобы задать переменные среды на основе конфигурации, профиля или среды.</span><span class="sxs-lookup"><span data-stu-id="c302a-408">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="c302a-409">Конфигурация IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-409">IIS configuration</span></span>

<span data-ttu-id="c302a-410">**Операционные системы семейства Windows Server**</span><span class="sxs-lookup"><span data-stu-id="c302a-410">**Windows Server operating systems**</span></span>

<span data-ttu-id="c302a-411">Включите роль сервера **Веб-сервер (IIS)** и настройте службы роли.</span><span class="sxs-lookup"><span data-stu-id="c302a-411">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="c302a-412">В меню **Управление** запустите мастер **Добавить роли и компоненты** или в окне **Диспетчер серверов** щелкните соответствующую ссылку.</span><span class="sxs-lookup"><span data-stu-id="c302a-412">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="c302a-413">На этапе **Роли сервера** установите флажок **Веб-сервер (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="c302a-413">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Роль "Веб-сервер (IIS)" выбрана на странице "Выбор ролей сервера".](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="c302a-415">После этапа выбора **компонентов** запускается этап выбора **служб роли** для веб-сервера (IIS).</span><span class="sxs-lookup"><span data-stu-id="c302a-415">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="c302a-416">Выберите нужные службы роли IIS или оставьте службы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c302a-416">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Службы роли по умолчанию, выбранные на странице "Выбор служб ролей".](index/_static/role-services-ws2016.png)

   <span data-ttu-id="c302a-418">**Проверка подлинности Windows (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c302a-418">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c302a-419">Чтобы включить проверку подлинности Windows, разверните такие узлы: **Веб-сервер** > **Безопасность**.</span><span class="sxs-lookup"><span data-stu-id="c302a-419">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="c302a-420">Выберите компонент **Проверка подлинности Windows**.</span><span class="sxs-lookup"><span data-stu-id="c302a-420">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c302a-421">Дополнительные сведения см. в статьях [Проверка подлинности Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) и [Настройка проверки подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-421">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c302a-422">**Протокол WebSocket (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c302a-422">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c302a-423">Протокол WebSocket поддерживается в ASP.NET Core начиная с версии 1.1.</span><span class="sxs-lookup"><span data-stu-id="c302a-423">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c302a-424">Чтобы включить протокол WebSocket, разверните такие узлы: **Веб-сервер** > **Разработка приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-424">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="c302a-425">Выберите компонент **Протокол WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="c302a-425">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c302a-426">Дополнительные сведения см. в разделе [Протокол WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="c302a-426">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c302a-427">Пройдите этап **Подтверждение**, чтобы установить роль и службы веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="c302a-427">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="c302a-428">После установки роли **Веб-сервер (IIS)** перезагружать сервер или службы IIS не требуется.</span><span class="sxs-lookup"><span data-stu-id="c302a-428">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="c302a-429">**Операционные системы Windows для настольных компьютеров**</span><span class="sxs-lookup"><span data-stu-id="c302a-429">**Windows desktop operating systems**</span></span>

<span data-ttu-id="c302a-430">Включите компоненты **Консоль управления IIS** и **Службы Интернета**.</span><span class="sxs-lookup"><span data-stu-id="c302a-430">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c302a-431">Последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).</span><span class="sxs-lookup"><span data-stu-id="c302a-431">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="c302a-432">Разверните узел **Службы IIS**.</span><span class="sxs-lookup"><span data-stu-id="c302a-432">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="c302a-433">Разверните узел **Средства управления веб-сайтом**.</span><span class="sxs-lookup"><span data-stu-id="c302a-433">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="c302a-434">Установите флажок **Консоль управления IIS**.</span><span class="sxs-lookup"><span data-stu-id="c302a-434">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="c302a-435">Установите флажок **Службы Интернета**.</span><span class="sxs-lookup"><span data-stu-id="c302a-435">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c302a-436">В группе **Службы Интернета** оставьте компоненты по умолчанию или настройте компоненты IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-436">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="c302a-437">**Проверка подлинности Windows (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c302a-437">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c302a-438">Чтобы включить проверку подлинности Windows, разверните такие узлы: **Службы Интернета** > **Безопасность**.</span><span class="sxs-lookup"><span data-stu-id="c302a-438">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="c302a-439">Выберите компонент **Проверка подлинности Windows**.</span><span class="sxs-lookup"><span data-stu-id="c302a-439">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c302a-440">Дополнительные сведения см. в статьях [Проверка подлинности Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) и [Настройка проверки подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-440">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c302a-441">**Протокол WebSocket (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c302a-441">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c302a-442">Протокол WebSocket поддерживается в ASP.NET Core начиная с версии 1.1.</span><span class="sxs-lookup"><span data-stu-id="c302a-442">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c302a-443">Чтобы включить протокол WebSocket, разверните такие узлы: **Службы Интернета** > **Компоненты разработки приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-443">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="c302a-444">Выберите компонент **Протокол WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="c302a-444">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c302a-445">Дополнительные сведения см. в разделе [Протокол WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="c302a-445">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c302a-446">Если во время установки IIS потребуется перезагрузка, перезагрузите систему.</span><span class="sxs-lookup"><span data-stu-id="c302a-446">If the IIS installation requires a restart, restart the system.</span></span>

![Группы "Консоль управления IIS" и "Службы Интернета" выбраны в окне "Компоненты Windows".](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="c302a-448">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="c302a-448">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="c302a-449">Установите *пакет размещения .NET Core* в размещающей системе.</span><span class="sxs-lookup"><span data-stu-id="c302a-449">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="c302a-450">В составе пакета устанавливаются среда выполнения .NET Core, библиотека .NET Core и [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c302a-450">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="c302a-451">Модуль позволяет запускать приложения ASP.NET Core под управлением IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-451">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c302a-452">Если пакет размещения устанавливается до установки служб IIS, его нужно восстановить.</span><span class="sxs-lookup"><span data-stu-id="c302a-452">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="c302a-453">После установки служб IIS запустите установщик пакета размещения еще раз.</span><span class="sxs-lookup"><span data-stu-id="c302a-453">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="c302a-454">Если пакет размещения устанавливается после установки 64-разрядной (x 64) версии .NET Core, пакеты SDK могут не отображаться (см. раздел [Пакеты SDK .NET Core не обнаружены](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="c302a-454">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="c302a-455">Сведения об устранении проблемы см. в статье <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="c302a-455">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="c302a-456">Прямая загрузка (текущая версия)</span><span class="sxs-lookup"><span data-stu-id="c302a-456">Direct download (current version)</span></span>

<span data-ttu-id="c302a-457">Скачайте установщик по следующей ссылке:</span><span class="sxs-lookup"><span data-stu-id="c302a-457">Download the installer using the following link:</span></span>

[<span data-ttu-id="c302a-458">Текущий установщик пакета размещения .NET Core (прямая загрузка)</span><span class="sxs-lookup"><span data-stu-id="c302a-458">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="c302a-459">Более ранние версии установщика</span><span class="sxs-lookup"><span data-stu-id="c302a-459">Earlier versions of the installer</span></span>

<span data-ttu-id="c302a-460">Получение более ранней версии установщика:</span><span class="sxs-lookup"><span data-stu-id="c302a-460">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="c302a-461">Перейдите на страницу [загрузки .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="c302a-461">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="c302a-462">Выберите требуемую версию .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-462">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="c302a-463">В столбце **Запуск приложений — среда выполнения** найдите строку, содержащую нужную версию среды выполнения .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-463">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="c302a-464">Скачайте установщик по ссылке **Hosting Bundle** (Пакет размещения).</span><span class="sxs-lookup"><span data-stu-id="c302a-464">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="c302a-465">Некоторые установщики содержат версии выпусков, которые достигли конца своего жизненного цикла и больше не поддерживаются корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="c302a-465">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="c302a-466">Дополнительные сведения см. в разделе [Политика поддержки](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="c302a-466">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="c302a-467">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="c302a-467">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="c302a-468">Запустите установщик на сервере.</span><span class="sxs-lookup"><span data-stu-id="c302a-468">Run the installer on the server.</span></span> <span data-ttu-id="c302a-469">При запуске установщика из командной оболочки администратора доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="c302a-469">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="c302a-470">`OPT_NO_ANCM=1`. Пропуск установки модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-470">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="c302a-471">`OPT_NO_RUNTIME=1`. Пропуск установки среды выполнения .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-471">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="c302a-472">Используется, когда на сервере размещаются только [автономные развертывания](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="c302a-472">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="c302a-473">`OPT_NO_SHAREDFX=1`. Пропуск установки общей платформы ASP.NET (среды выполнения ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="c302a-473">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="c302a-474">Используется, когда на сервере размещаются только [автономные развертывания](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="c302a-474">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="c302a-475">`OPT_NO_X86=1`. Пропуск установки сред выполнения x86.</span><span class="sxs-lookup"><span data-stu-id="c302a-475">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="c302a-476">Этот параметр следует использовать, если вы наверняка не будете размещать 32-разрядные приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-476">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="c302a-477">Если есть хоть малейшая возможность, что в будущем придется размещать и 32-разрядные, и 64-разрядные приложения, не указывайте этот параметр и установите обе среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="c302a-477">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="c302a-478">`OPT_NO_SHARED_CONFIG_CHECK=1`. Отключение проверки использования общей конфигурации IIS, если файл общей конфигурации (*applicationHost.config*) находится на том же компьютере, что и установка IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="c302a-479">*Доступен только для пакетных установщиков размещения ASP.NET Core 2.2 или более поздней версии.*</span><span class="sxs-lookup"><span data-stu-id="c302a-479">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="c302a-480">Для получения дополнительной информации см. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="c302a-480">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="c302a-481">Перезапустите систему или выполните в командной оболочке следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c302a-481">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="c302a-482">Перезапуск служб IIS позволит обнаружить внесенные установщиком изменения в системном пути, который является переменной среды.</span><span class="sxs-lookup"><span data-stu-id="c302a-482">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="c302a-483">ASP.NET Core не наследует поведение наката для выпусков исправлений пакетов общей платформы.</span><span class="sxs-lookup"><span data-stu-id="c302a-483">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="c302a-484">После обновления общей платформы путем установки нового пакета размещения перезапустите систему или выполните в командной оболочке следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c302a-484">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="c302a-485">Сведения об общей конфигурации IIS см. в разделе [Модуль ASP.NET Core с общей конфигурацией IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="c302a-485">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="c302a-486">Установка веб-развертывания при публикации с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c302a-486">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="c302a-487">При развертывании приложений на серверах с помощью [веб-развертывания](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) установите на сервере последнюю версию веб-развертывания.</span><span class="sxs-lookup"><span data-stu-id="c302a-487">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="c302a-488">Чтобы установить веб-развертывание, можно использовать [установщик веб-платформы (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) или получить установщик непосредственно в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="c302a-488">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="c302a-489">Предпочтительный метод — использовать WebPI.</span><span class="sxs-lookup"><span data-stu-id="c302a-489">The preferred method is to use WebPI.</span></span> <span data-ttu-id="c302a-490">WebPI обеспечивает автономную установку и настройку поставщиков размещения.</span><span class="sxs-lookup"><span data-stu-id="c302a-490">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="c302a-491">Создание сайта IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-491">Create the IIS site</span></span>

1. <span data-ttu-id="c302a-492">В размещающей системе создайте папку, в которой будут храниться файлы и папки опубликованного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-492">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="c302a-493">На следующем этапе путь к папке предоставляется IIS как физический путь к приложению.</span><span class="sxs-lookup"><span data-stu-id="c302a-493">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="c302a-494">Дополнительные сведения о папке развертывания и структуре файлов приложения см. в статье <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="c302a-494">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="c302a-495">В окне диспетчера IIS на панели **Подключения** разверните узел сервера.</span><span class="sxs-lookup"><span data-stu-id="c302a-495">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="c302a-496">Щелкните правой кнопкой мыши папку **Сайты**.</span><span class="sxs-lookup"><span data-stu-id="c302a-496">Right-click the **Sites** folder.</span></span> <span data-ttu-id="c302a-497">В контекстном меню выберите пункт **Добавить веб-сайт**.</span><span class="sxs-lookup"><span data-stu-id="c302a-497">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="c302a-498">Укажите имя в поле **Имя сайта** и задайте значение в поле **Физический путь** для созданной папки развертывания приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-498">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="c302a-499">Укажите конфигурацию **привязки** и нажмите кнопку **ОК**, чтобы создать веб-сайт.</span><span class="sxs-lookup"><span data-stu-id="c302a-499">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![В окне "Добавить веб-сайт" укажите имя сайта, физический путь и имя узла.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="c302a-501">**Не используйте** привязки с подстановочными знаками (`http://*:80/` и `http://+:80`) на верхнем уровне.</span><span class="sxs-lookup"><span data-stu-id="c302a-501">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="c302a-502">Это может создать уязвимость и поставить ваше приложение под угрозу.</span><span class="sxs-lookup"><span data-stu-id="c302a-502">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="c302a-503">Сюда относятся и строгие, и нестрогие подстановочные знаки.</span><span class="sxs-lookup"><span data-stu-id="c302a-503">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="c302a-504">Вместо этого используйте имена узлов в явном виде.</span><span class="sxs-lookup"><span data-stu-id="c302a-504">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="c302a-505">Привязки с подстановочными знаками на уровне дочерних доменов (например `*.mysub.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость).</span><span class="sxs-lookup"><span data-stu-id="c302a-505">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="c302a-506">Дополнительные сведения см. в документе [rfc7230, раздел 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="c302a-506">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="c302a-507">Разверните узел сервера и выберите **Пулы приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-507">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="c302a-508">Щелкните правой кнопкой мыши пул приложений сайта и в контекстном меню выберите пункт **Основные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-508">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="c302a-509">В окне **Изменение пула приложений** задайте для параметра **Версия среды CLR .NET** значение **Без управляемого кода**.</span><span class="sxs-lookup"><span data-stu-id="c302a-509">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Выбор значения "Без управляемого кода" для параметра "Версия среды CLR .NET".](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="c302a-511">ASP.NET Core выполняется в отдельном процессе и управляет средой выполнения.</span><span class="sxs-lookup"><span data-stu-id="c302a-511">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="c302a-512">Для ASP.NET Core не требуется загрузка классической среды CLR (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="c302a-512">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="c302a-513">Для размещения приложения в рабочем процессе запускается CoreCLR для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-513">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="c302a-514">Задавать для параметра **Версия среды CLR .NET** значение **Без управляемого кода** необязательно, но рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="c302a-514">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="c302a-515">*ASP.NET Core 2.2 или более поздней версии*:</span><span class="sxs-lookup"><span data-stu-id="c302a-515">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="c302a-516">Для 32-разрядного (x86) [автономного развертывания](/dotnet/core/deploying/#self-contained-deployments-scd), опубликованного с помощью 32-разрядного пакета SDK, в котором используется [модель размещения в процессе](#in-process-hosting-model), включите пул приложений для 32-разрядной архитектуры.</span><span class="sxs-lookup"><span data-stu-id="c302a-516">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="c302a-517">В диспетчере IIS перейдите в раздел **Пулы приложений** на боковой панели **Подключения**.</span><span class="sxs-lookup"><span data-stu-id="c302a-517">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="c302a-518">Выберите пул приложений приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-518">Select the app's Application Pool.</span></span> <span data-ttu-id="c302a-519">На боковой панели **Действия** выберите **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-519">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="c302a-520">Установите для параметра **Включить 32-разрядные приложения** значение `True`.</span><span class="sxs-lookup"><span data-stu-id="c302a-520">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="c302a-521">для 64-разрядного (x64) [автономного развертывания](/dotnet/core/deploying/#self-contained-deployments-scd), в котором используется [модель размещения в процессе](#in-process-hosting-model), отключите пул приложений для 32-разрядных (x86) процессов.</span><span class="sxs-lookup"><span data-stu-id="c302a-521">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="c302a-522">В диспетчере IIS перейдите в раздел **Пулы приложений** на боковой панели **Подключения**.</span><span class="sxs-lookup"><span data-stu-id="c302a-522">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="c302a-523">Выберите пул приложений приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-523">Select the app's Application Pool.</span></span> <span data-ttu-id="c302a-524">На боковой панели **Действия** выберите **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-524">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="c302a-525">Установите для параметра **Включить 32-разрядные приложения** значение `False`.</span><span class="sxs-lookup"><span data-stu-id="c302a-525">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="c302a-526">Убедитесь в том, что удостоверение модели процесса имеет соответствующие разрешения.</span><span class="sxs-lookup"><span data-stu-id="c302a-526">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="c302a-527">При изменении удостоверения по умолчанию для пула приложений (**Модель процесса** >  **Identity** ) с **ApplicationPoolIdentity** на другое, убедитесь, что у нового удостоверения есть соответствующие разрешения для доступа к папке приложения, базе данных и другим необходимым ресурсам.</span><span class="sxs-lookup"><span data-stu-id="c302a-527">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="c302a-528">Например, пулу приложений требуются права на чтение и запись в папках, в которых приложение считывает и записывает файлы.</span><span class="sxs-lookup"><span data-stu-id="c302a-528">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="c302a-529">**Настройка проверки подлинности Windows (необязательный этап)**</span><span class="sxs-lookup"><span data-stu-id="c302a-529">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="c302a-530">См. статью [Configure Windows authentication in an ASP.NET Core app](xref:security/authentication/windowsauth) (Настройка проверки подлинности Windows в приложении ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="c302a-530">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="c302a-531">Развертывание приложения</span><span class="sxs-lookup"><span data-stu-id="c302a-531">Deploy the app</span></span>

<span data-ttu-id="c302a-532">Разверните приложение в папке IIS, **физический путь** к которой вы указали в рамках раздела [Создание сайта IIS](#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="c302a-532">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="c302a-533">Рекомендуется выполнять [веб-развертывание](/iis/publish/using-web-deploy/introduction-to-web-deploy), но есть несколько других способов переместить приложение из папки *публикации* проекта в папку развертывания размещающей системы.</span><span class="sxs-lookup"><span data-stu-id="c302a-533">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="c302a-534">Веб-развертывание с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c302a-534">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="c302a-535">Сведения о создании профиля публикации для веб-развертывания см. в статье [Профили публикации Visual Studio для развертывания приложений ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="c302a-535">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="c302a-536">Если поставщик услуг размещения предоставляет профиль публикации или позволяет его создать, скачайте этот профиль и импортируйте его с помощью диалогового окна **Публикация** в Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c302a-536">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Диалоговое окно "Публикация"](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="c302a-538">Веб-развертывание вне Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c302a-538">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="c302a-539">[Веб-развертывание](/iis/publish/using-web-deploy/introduction-to-web-deploy) можно также использовать вне Visual Studio с помощью командной строки.</span><span class="sxs-lookup"><span data-stu-id="c302a-539">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="c302a-540">Дополнительные сведения см. в статье [Средство веб-развертывания](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="c302a-540">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="c302a-541">Альтернативы веб-развертыванию</span><span class="sxs-lookup"><span data-stu-id="c302a-541">Alternatives to Web Deploy</span></span>

<span data-ttu-id="c302a-542">Переместить приложение в размещающую систему можно несколькими способами: с помощью копирования вручную, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) или [PowerShell](/powershell/).</span><span class="sxs-lookup"><span data-stu-id="c302a-542">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="c302a-543">Дополнительные сведения о развертывании ASP.NET Core в службах IIS см. в разделе [Ресурсы развертывания для администраторов IIS](#deployment-resources-for-iis-administrators).</span><span class="sxs-lookup"><span data-stu-id="c302a-543">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="c302a-544">Обзор веб-сайта</span><span class="sxs-lookup"><span data-stu-id="c302a-544">Browse the website</span></span>

<span data-ttu-id="c302a-545">Когда приложение будет развернуто в размещающей системе, выполните запрос к одной из общедоступных конечных точек приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-545">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="c302a-546">В приведенном ниже примере сайт привязан к **имени узла** IIS `www.mysite.com` в **порте** `80`.</span><span class="sxs-lookup"><span data-stu-id="c302a-546">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="c302a-547">Запрос отправляется по адресу `http://www.mysite.com`:</span><span class="sxs-lookup"><span data-stu-id="c302a-547">A request is made to `http://www.mysite.com`:</span></span>

![Начальная страница IIS, загруженная в браузере Microsoft Edge.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="c302a-549">Заблокированные файлы развертывания</span><span class="sxs-lookup"><span data-stu-id="c302a-549">Locked deployment files</span></span>

<span data-ttu-id="c302a-550">Во время выполнения приложения файлы в папке развертывания блокируются.</span><span class="sxs-lookup"><span data-stu-id="c302a-550">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="c302a-551">Заблокированные файлы невозможно перезаписать во время развертывания.</span><span class="sxs-lookup"><span data-stu-id="c302a-551">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="c302a-552">Чтобы снять блокировку с файлов в развертывании, остановите пул приложений с помощью **одного** из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="c302a-552">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="c302a-553">Запустите веб-развертывание и добавьте ссылку на `Microsoft.NET.Sdk.Web` в файл проекта.</span><span class="sxs-lookup"><span data-stu-id="c302a-553">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="c302a-554">Файл *app_offline.htm* помещается в корень каталога веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-554">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="c302a-555">Если файл присутствует, модуль ASP.NET Core корректно завершает работу приложения и обслуживает файл *app_offline.htm* во время развертывания.</span><span class="sxs-lookup"><span data-stu-id="c302a-555">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="c302a-556">Дополнительные сведения см. в разделе [Справочник по конфигурации модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="c302a-556">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="c302a-557">Вручную остановите пул приложений в диспетчере служб IIS на сервере.</span><span class="sxs-lookup"><span data-stu-id="c302a-557">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="c302a-558">С помощью PowerShell удалите *app_offline.htm* (требуется PowerShell 5 или более поздняя версия):</span><span class="sxs-lookup"><span data-stu-id="c302a-558">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="c302a-559">Защита данных</span><span class="sxs-lookup"><span data-stu-id="c302a-559">Data protection</span></span>

<span data-ttu-id="c302a-560">[Стек защиты данных ASP.NET Core](xref:security/data-protection/introduction) используют несколько [ПО промежуточного слоя](xref:fundamentals/middleware/index) ASP.NET Core, включая ПО, которое применяется для аутентификации.</span><span class="sxs-lookup"><span data-stu-id="c302a-560">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="c302a-561">Даже если API-интерфейсы защиты данных не вызываются из пользовательского кода, защиту данных следует настроить для создания постоянного [хранилища криптографических ключей](xref:security/data-protection/implementation/key-management). Это можно сделать с помощью скрипта развертывания или в пользовательском коде.</span><span class="sxs-lookup"><span data-stu-id="c302a-561">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="c302a-562">Если защита данных не настроена, ключи хранятся в памяти и удаляются при перезапуске приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-562">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="c302a-563">Если набор ключей хранится в памяти, при перезапуске приложения происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="c302a-563">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="c302a-564">Все токены аутентификации, использующие файлы cookie, становятся недействительными.</span><span class="sxs-lookup"><span data-stu-id="c302a-564">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="c302a-565">При выполнении следующего запроса пользователю требуется выполнить вход снова.</span><span class="sxs-lookup"><span data-stu-id="c302a-565">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="c302a-566">Все данные, защищенные с помощью набора ключей, больше не могут быть расшифрованы.</span><span class="sxs-lookup"><span data-stu-id="c302a-566">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="c302a-567">Это могут быть [токены CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) и [файлы cookie временных данных ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="c302a-567">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="c302a-568">Чтобы настроить защиту данных в службах IIS для хранения набора ключей, воспользуйтесь **одним** из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="c302a-568">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="c302a-569">**Создание раздела реестра для защиты данных**.</span><span class="sxs-lookup"><span data-stu-id="c302a-569">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="c302a-570">Ключи защиты данных, используемые приложениями ASP.NET Core, хранятся во внешнем для приложений реестре.</span><span class="sxs-lookup"><span data-stu-id="c302a-570">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="c302a-571">Чтобы хранить эти ключи для определенного приложения, нужно создать разделы реестра для пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-571">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="c302a-572">При автономной установке служб IIS, не поддерживающей веб-ферму, можно выполнить [скрипт PowerShell Provision-AutoGenKeys.ps1 для защиты данных](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) применительно к каждому пулу приложений, в который входит приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-572">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="c302a-573">Этот скрипт создает раздел в реестре HKLM, который будет доступен только для учетной записи рабочего процесса пула приложений, к которому относится соответствующее приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-573">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="c302a-574">Неактивные ключи шифруются с помощью API защиты данных с ключом компьютера.</span><span class="sxs-lookup"><span data-stu-id="c302a-574">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="c302a-575">В случаях, когда используется веб-ферма, в приложении можно настроить UNC-путь, по которому это приложение будет хранить набор ключей для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="c302a-575">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="c302a-576">По умолчанию ключи защиты данных не шифруются.</span><span class="sxs-lookup"><span data-stu-id="c302a-576">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="c302a-577">Разрешения на доступ к файлам в сетевой папке должны быть предоставлены только учетной записи Windows, с помощью которой выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-577">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="c302a-578">Для защиты неактивных ключей можно использовать сертификат X509.</span><span class="sxs-lookup"><span data-stu-id="c302a-578">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="c302a-579">Рассмотрите возможность реализации механизма, позволяющего пользователям отправлять сертификаты: поместите сертификаты в хранилище доверенных сертификатов пользователя и обеспечьте их доступность на всех компьютерах, где выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-579">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="c302a-580">Дополнительные сведения см. в разделе [Настройка защиты данных в ASP.NET Core](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="c302a-580">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="c302a-581">**Настройка пула приложений IIS для загрузки профиля пользователя**.</span><span class="sxs-lookup"><span data-stu-id="c302a-581">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="c302a-582">Этот параметр находится на странице **Дополнительные параметры** пула приложений в разделе **Модель процесса**.</span><span class="sxs-lookup"><span data-stu-id="c302a-582">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="c302a-583">Задайте для параметра **Загрузить профиль пользователя** значение `True`.</span><span class="sxs-lookup"><span data-stu-id="c302a-583">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="c302a-584">Если задать значение `True`, ключи будут храниться в каталоге профиля пользователя и защищаться с помощью API защиты данных и ключа на уровне учетной записи пользователя.</span><span class="sxs-lookup"><span data-stu-id="c302a-584">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="c302a-585">Ключи хранятся в папке *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="c302a-585">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="c302a-586">Также необходимо включить атрибут [setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-586">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="c302a-587">Значением свойства `setProfileEnvironment` по умолчанию является `true`.</span><span class="sxs-lookup"><span data-stu-id="c302a-587">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="c302a-588">В некоторых сценариях (например, в ОС Windows) для параметра `setProfileEnvironment` установлено значение `false`.</span><span class="sxs-lookup"><span data-stu-id="c302a-588">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="c302a-589">Если ключи не хранятся в каталоге профиля пользователя:</span><span class="sxs-lookup"><span data-stu-id="c302a-589">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="c302a-590">Перейдите к папке *%windir%/system32/inetsrv/config*.</span><span class="sxs-lookup"><span data-stu-id="c302a-590">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="c302a-591">Откройте файл *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="c302a-591">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="c302a-592">Найдите элемент `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .</span><span class="sxs-lookup"><span data-stu-id="c302a-592">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="c302a-593">Убедитесь, что атрибут `setProfileEnvironment` отсутствует и установлено значение по умолчанию `true`, или же явно задайте для атрибута значение `true`.</span><span class="sxs-lookup"><span data-stu-id="c302a-593">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="c302a-594">**Использование файловой системы в качестве хранилища набора ключей**.</span><span class="sxs-lookup"><span data-stu-id="c302a-594">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="c302a-595">Измените код приложения так, чтобы [в качестве хранилища набора ключей использовалась файловая система](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="c302a-595">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="c302a-596">Для защиты набора ключей используйте доверенный сертификат X509.</span><span class="sxs-lookup"><span data-stu-id="c302a-596">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="c302a-597">Если сертификат — самозаверяющий, поместите его в доверенное корневое хранилище.</span><span class="sxs-lookup"><span data-stu-id="c302a-597">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="c302a-598">При использовании служб IIS в веб-ферме:</span><span class="sxs-lookup"><span data-stu-id="c302a-598">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="c302a-599">используйте общую папку, которая доступна всем компьютерам;</span><span class="sxs-lookup"><span data-stu-id="c302a-599">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="c302a-600">разверните сертификат X509 на каждом компьютере;</span><span class="sxs-lookup"><span data-stu-id="c302a-600">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="c302a-601">настройте [защиту данных в коде](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="c302a-601">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="c302a-602">**Настройка политики защиты данных на уровне компьютера**.</span><span class="sxs-lookup"><span data-stu-id="c302a-602">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="c302a-603">Система защиты данных обеспечивает ограниченную поддержку задания [политики по умолчанию на уровне компьютера](xref:security/data-protection/configuration/machine-wide-policy) для всех приложений, использующих интерфейсы API защиты данных.</span><span class="sxs-lookup"><span data-stu-id="c302a-603">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="c302a-604">Для получения дополнительной информации см. <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="c302a-604">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="c302a-605">Виртуальные каталоги</span><span class="sxs-lookup"><span data-stu-id="c302a-605">Virtual Directories</span></span>

<span data-ttu-id="c302a-606">[Виртуальные каталоги IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) не поддерживаются в приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-606">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="c302a-607">Приложение может размещаться как [вложенное](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="c302a-607">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="c302a-608">Вложенные приложения</span><span class="sxs-lookup"><span data-stu-id="c302a-608">Sub-applications</span></span>

<span data-ttu-id="c302a-609">Приложение ASP.NET Core можно разместить как [вложенное приложение IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="c302a-609">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="c302a-610">Путь к вложенному приложению становится частью URL-адреса главного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-610">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="c302a-611">В ссылках на статический ресурс во вложенном приложении следует использовать нотацию `~/`.</span><span class="sxs-lookup"><span data-stu-id="c302a-611">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="c302a-612">Такая нотация активирует [вспомогательную функцию тега](xref:mvc/views/tag-helpers/intro) для добавления свойства pathbase вложенного приложения в начало отображаемой относительной ссылки.</span><span class="sxs-lookup"><span data-stu-id="c302a-612">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="c302a-613">Для вложенного приложения с путем `/subapp_path` ссылка на изображение `src="~/image.png"` отображается как `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="c302a-613">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="c302a-614">ПО промежуточного слоя для обработки статических файлов в главном приложении не обрабатывает такой запрос статического файла.</span><span class="sxs-lookup"><span data-stu-id="c302a-614">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="c302a-615">Запрос обрабатывается соответствующим ПО вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-615">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="c302a-616">Если атрибуту `src` статического ресурса присваивается абсолютный путь (например, `src="/image.png"`), ссылка отображается без свойства pathbase вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-616">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="c302a-617">ПО промежуточного слоя для обработки статических файлов в главном приложении пытается найти ресурс в [корневом веб-каталоге](xref:fundamentals/index#web-root) главного приложения, что приводит к ошибке *404 — Not Found* (не найдено), кроме случаев, когда статический ресурс доступен из главного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-617">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="c302a-618">Для размещения приложения ASP.NET Core в качестве приложения, вложенного в другое приложение ASP.NET Core, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="c302a-618">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="c302a-619">Создайте пул приложений для вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-619">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="c302a-620">Установите для параметра **Версия среды CLR .NET** значение **Без управляемого кода**, так как для размещения приложения в рабочем процессе запускается CoreCLR для .NET Core, а не среда CRL для настольных ПК (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="c302a-620">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="c302a-621">Добавьте корневой веб-сайт в диспетчере служб IIS с вложенным приложением в папке внутри корневого веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="c302a-621">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="c302a-622">Щелкните правой кнопкой мыши папку вложенного приложения в диспетчере служб IIS и выберите **Преобразовать в приложение**.</span><span class="sxs-lookup"><span data-stu-id="c302a-622">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="c302a-623">В диалоговом окне **Добавление приложения** нажмите кнопку **Выбрать**, чтобы назначить созданный **пул приложений** вложенному приложению.</span><span class="sxs-lookup"><span data-stu-id="c302a-623">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="c302a-624">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c302a-624">Select **OK**.</span></span>

<span data-ttu-id="c302a-625">Назначение отдельного пула приложений вложенному приложению является обязательным при использовании модели внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="c302a-625">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="c302a-626">Дополнительные сведения о внутрипроцессной модели размещения и настройке модуля ASP.NET Core см. в статье <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c302a-626">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="c302a-627">Настройка служб IIS с помощью файла web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-627">Configuration of IIS with web.config</span></span>

<span data-ttu-id="c302a-628">Конфигурация IIS зависит от `<system.webServer>` раздела *web.config* для сценариев IIS, предназначенных для работы приложений ASP.NET Core с помощью модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-628">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="c302a-629">Например, конфигурация IIS работает для динамического сжатия.</span><span class="sxs-lookup"><span data-stu-id="c302a-629">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="c302a-630">Если в службах IIS на уровне сервера настроено динамическое сжатие, элемент `<urlCompression>` в файле *web.config* приложения может отключить это сжатие для приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-630">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="c302a-631">Дополнительные сведения см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="c302a-631">For more information, see the following topics:</span></span>

* [<span data-ttu-id="c302a-632">Справочник по настройке для \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="c302a-632">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="c302a-633">Сведения о настройке переменных среды для отдельных приложений, выполняющихся в изолированных пулах приложений (такая возможность поддерживается в службах IIS начиная с версии 10.0), см. в справочной документации по службам IIS, в частности в разделе *Команда AppCmd.exe* статьи [Переменные среды \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="c302a-633">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="c302a-634">Разделы конфигурации файла web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-634">Configuration sections of web.config</span></span>

<span data-ttu-id="c302a-635">Разделы конфигурации приложений ASP.NET 4.x в файле *web.config* не используются для конфигурации приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-635">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="c302a-636">Для настройки приложений ASP.NET Core используются другие поставщики конфигураций.</span><span class="sxs-lookup"><span data-stu-id="c302a-636">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="c302a-637">Дополнительные сведения см. в разделе [Конфигурация](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c302a-637">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="c302a-638">Пулы приложений</span><span class="sxs-lookup"><span data-stu-id="c302a-638">Application Pools</span></span>

<span data-ttu-id="c302a-639">Модель размещения определяет изоляцию пула приложений:</span><span class="sxs-lookup"><span data-stu-id="c302a-639">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="c302a-640">Внутрипроцессное размещение: Приложения должны выполняться в отдельных пулах.</span><span class="sxs-lookup"><span data-stu-id="c302a-640">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="c302a-641">Размещение вне процесса: Рекомендуем изолировать приложения друг от друга, запуская каждое из них в собственном пуле.</span><span class="sxs-lookup"><span data-stu-id="c302a-641">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="c302a-642">В диалоговом окне **Добавление веб-сайта** IIS на каждое приложение по умолчанию задан один пул приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-642">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="c302a-643">Если указано **Имя сайта**, оно автоматически переносится в текстовое поле **Пул приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-643">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="c302a-644">При добавлении веб-сайта создается пул приложений с именем сайта.</span><span class="sxs-lookup"><span data-stu-id="c302a-644">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="c302a-645">Identity пула приложений</span><span class="sxs-lookup"><span data-stu-id="c302a-645">Application Pool Identity</span></span>

<span data-ttu-id="c302a-646">Учетная запись удостоверения пула приложений позволяет запускать приложение от имени уникальной учетной записи, не создавая домены или локальные учетные записи и не управляя ими.</span><span class="sxs-lookup"><span data-stu-id="c302a-646">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="c302a-647">В службах IIS начиная с версии 8.0 рабочий процесс администратора IIS (WAS) создает виртуальную учетную запись с именем нового пула приложений и по умолчанию выполняет рабочие процессы пула приложений с помощью этой учетной записи.</span><span class="sxs-lookup"><span data-stu-id="c302a-647">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="c302a-648">В консоли управления IIS в окне **Дополнительные параметры** пула приложений для **Identity** необходимо выбрать **ApplicationPoolIdentity**.</span><span class="sxs-lookup"><span data-stu-id="c302a-648">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Диалоговое окно дополнительных параметров для пула приложений](index/_static/apppool-identity.png)

<span data-ttu-id="c302a-650">Процесс управления IIS создает в системе безопасности Windows безопасный идентификатор с именем пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-650">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="c302a-651">С помощью этого идентификатора можно защищать ресурсы,</span><span class="sxs-lookup"><span data-stu-id="c302a-651">Resources can be secured using this identity.</span></span> <span data-ttu-id="c302a-652">но он не является реальной учетной записью и не отображается в консоли управления пользователями Windows.</span><span class="sxs-lookup"><span data-stu-id="c302a-652">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="c302a-653">Если рабочему процессу IIS требуется предоставить доступ к приложению с повышенными правами, измените список управления доступом (ACL) для каталога, содержащего приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-653">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="c302a-654">Откройте проводник и перейдите к каталогу.</span><span class="sxs-lookup"><span data-stu-id="c302a-654">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="c302a-655">Щелкните каталог правой кнопкой мыши и выберите пункт **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="c302a-655">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="c302a-656">На вкладке **Безопасность** нажмите кнопку **Изменить**, а затем — кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c302a-656">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="c302a-657">Нажмите кнопку **Размещение** и выберите систему.</span><span class="sxs-lookup"><span data-stu-id="c302a-657">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="c302a-658">В поле **Введите имена выбираемых объектов** введите **IIS AppPool\\<имя_пула_приложений>** .</span><span class="sxs-lookup"><span data-stu-id="c302a-658">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="c302a-659">Нажмите кнопку **Проверить имена**.</span><span class="sxs-lookup"><span data-stu-id="c302a-659">Select the **Check Names** button.</span></span> <span data-ttu-id="c302a-660">В случае с *DefaultAppPool* проверьте имена с помощью **IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="c302a-660">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="c302a-661">После нажатия кнопки **Проверить имена** в области имен объектов отобразится значение **DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="c302a-661">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="c302a-662">Вручную ввести имя пула приложений в области имен объектов нельзя.</span><span class="sxs-lookup"><span data-stu-id="c302a-662">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="c302a-663">При поиске имени объекта используйте формат **IIS AppPool\\<имя_пула_приложений>** .</span><span class="sxs-lookup"><span data-stu-id="c302a-663">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Диалоговое окно "Выбор пользователей или групп" для папки приложения. До нажатия кнопки "Проверить имена" в области имен объектов к строке IIS AppPool\" добавилось имя пула приложений, DefaultAppPool.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="c302a-665">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c302a-665">Select **OK**.</span></span>

   ![Диалоговое окно "Выбор пользователей или групп" для папки приложения. После нажатия кнопки "Проверить имена" в области имен объектов отображается имя пула приложений, DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="c302a-667">Разрешения на чтение и выполнение предоставляются по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c302a-667">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="c302a-668">При необходимости предоставьте дополнительные разрешения.</span><span class="sxs-lookup"><span data-stu-id="c302a-668">Provide additional permissions as needed.</span></span>

<span data-ttu-id="c302a-669">Кроме того, доступ можно предоставить через командную строку, используя средство **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="c302a-669">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="c302a-670">В случае с *DefaultAppPool* выполняется такая команда:</span><span class="sxs-lookup"><span data-stu-id="c302a-670">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="c302a-671">Дополнительные сведения об ICACLS см. [здесь](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="c302a-671">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="c302a-672">Поддержка HTTP/2</span><span class="sxs-lookup"><span data-stu-id="c302a-672">HTTP/2 support</span></span>

<span data-ttu-id="c302a-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html) поддерживается в ASP.NET Core для следующих сценариев развертывания IIS:</span><span class="sxs-lookup"><span data-stu-id="c302a-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="c302a-674">Внутрипроцессно</span><span class="sxs-lookup"><span data-stu-id="c302a-674">In-process</span></span>
  * <span data-ttu-id="c302a-675">Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="c302a-675">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c302a-676">Подключение TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="c302a-676">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="c302a-677">Внепроцессно</span><span class="sxs-lookup"><span data-stu-id="c302a-677">Out-of-process</span></span>
  * <span data-ttu-id="c302a-678">Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="c302a-678">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c302a-679">Подключения к пограничным серверам, открытых для общего доступа, выполняются по протоколу HTTP/2, а подключения к [серверу Kestrel](xref:fundamentals/servers/kestrel) через обратный прокси-сервер — по HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c302a-679">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="c302a-680">Подключение TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="c302a-680">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c302a-681">При внутрипроцессном развертывании и установленном подключении HTTP/2 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="c302a-681">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="c302a-682">При внепроцессном развертывании и установленном подключении HTTP/2 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="c302a-682">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="c302a-683">Дополнительные сведения о внутрипроцессной и внепроцессной моделях размещения см. в статье <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c302a-683">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="c302a-684">Протокол HTTP/2 включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c302a-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="c302a-685">Если не удается установить подключение HTTP/2, применяется резервный вариант HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c302a-685">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="c302a-686">Дополнительные сведения о настройке HTTP/2 для развертываний IIS см. в статье [об HTTP/2 в IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="c302a-686">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="c302a-687">Предварительные запросы CORS</span><span class="sxs-lookup"><span data-stu-id="c302a-687">CORS preflight requests</span></span>

<span data-ttu-id="c302a-688">*Этот раздел относится только к приложениям ASP.NET Core, предназначенным для .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="c302a-688">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="c302a-689">Для приложения ASP.NET Core, предназначенного для .NET Framework, параметры OPTIONS не передаются в приложение по умолчанию в службах IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-689">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="c302a-690">Чтобы узнать, как настроить обработчики приложения IIS в файле *web.config* для передачи запросов OPTIONS, см. раздел [Enable cross-origin requests in ASP.NET Web API 2. How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works) (Включение запросов CORS в ASP.NET Web API 2. Принцип работы CORS).</span><span class="sxs-lookup"><span data-stu-id="c302a-690">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="c302a-691">Модуль инициализации приложений и время ожидания в режиме простоя</span><span class="sxs-lookup"><span data-stu-id="c302a-691">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="c302a-692">Размещение в IIS с помощью модуля ASP.NET Core версии 2:</span><span class="sxs-lookup"><span data-stu-id="c302a-692">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="c302a-693">[Модуль инициализации приложений](#application-initialization-module). Приложение, размещенное [в процессе](#in-process-hosting-model) или [вне процесса](#out-of-process-hosting-model), можно настроить на автоматический запуск при перезапуске рабочего процесса или сервера.</span><span class="sxs-lookup"><span data-stu-id="c302a-693">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="c302a-694">[Время ожидания в режиме простоя](#idle-timeout). Приложение, размещенное [в процессе](#in-process-hosting-model), можно настроить на игнорирование времени ожидания в периоды неактивности.</span><span class="sxs-lookup"><span data-stu-id="c302a-694">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="c302a-695">Модуль инициализации приложений</span><span class="sxs-lookup"><span data-stu-id="c302a-695">Application Initialization Module</span></span>

<span data-ttu-id="c302a-696">*Применяется к приложениям, размещенным в процессе и вне процесса.*</span><span class="sxs-lookup"><span data-stu-id="c302a-696">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="c302a-697">Функция [инициализации приложений](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) в IIS отправляет в приложение HTTP-запрос при запуске или перезапуске пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-697">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="c302a-698">Этот запрос инициирует запуск приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-698">The request triggers the app to start.</span></span> <span data-ttu-id="c302a-699">По умолчанию IIS отправляет запрос к корневому URL-адресу приложения (`/`) для его инициализации (подробные сведения о конфигурации см. в [дополнительных ресурсах](#application-initialization-module-and-idle-timeout-additional-resources)).</span><span class="sxs-lookup"><span data-stu-id="c302a-699">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="c302a-700">Убедитесь, что включена роль инициализации приложения IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-700">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="c302a-701">На настольных компьютерах с Windows 7 или более поздней версии при локальном использовании IIS:</span><span class="sxs-lookup"><span data-stu-id="c302a-701">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="c302a-702">Последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).</span><span class="sxs-lookup"><span data-stu-id="c302a-702">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="c302a-703">Откройте **Службы IIS** > **Службы Интернета** > **Компоненты разработки приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-703">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="c302a-704">Установите флажок **Инициализация приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-704">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="c302a-705">В Windows Server 2008 R2 и более поздней версии:</span><span class="sxs-lookup"><span data-stu-id="c302a-705">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="c302a-706">Откройте **Мастер добавления ролей и компонентов**.</span><span class="sxs-lookup"><span data-stu-id="c302a-706">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="c302a-707">На панели **Выбор служб ролей** разверните узел **Разработка приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-707">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="c302a-708">Установите флажок **Инициализация приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-708">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="c302a-709">Чтобы включить модуль инициализации приложений для сайта, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="c302a-709">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="c302a-710">При использовании диспетчера IIS:</span><span class="sxs-lookup"><span data-stu-id="c302a-710">Using IIS Manager:</span></span>

  1. <span data-ttu-id="c302a-711">Выберите **Пулы приложений** на панели **Подключения**.</span><span class="sxs-lookup"><span data-stu-id="c302a-711">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="c302a-712">Щелкните пул приложений в списке правой кнопкой мыши и выберите **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-712">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="c302a-713">Для **режима запуска** по умолчанию задано значение **OnDemand**.</span><span class="sxs-lookup"><span data-stu-id="c302a-713">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="c302a-714">Выберите для параметра **Режим запуска** значение **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="c302a-714">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="c302a-715">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c302a-715">Select **OK**.</span></span>
  1. <span data-ttu-id="c302a-716">Откройте узел **Сайты** на панели **Подключения**.</span><span class="sxs-lookup"><span data-stu-id="c302a-716">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="c302a-717">Щелкните приложение правой кнопкой мыши и выберите **Управление веб-сайтом** > **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-717">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="c302a-718">По умолчанию для параметра **Предварительная загрузка включена** установлено значение **False**.</span><span class="sxs-lookup"><span data-stu-id="c302a-718">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="c302a-719">Для параметра **Предварительная загрузка включена** выберите значение **True**.</span><span class="sxs-lookup"><span data-stu-id="c302a-719">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="c302a-720">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c302a-720">Select **OK**.</span></span>

* <span data-ttu-id="c302a-721">Откройте файл *web.config* и добавьте элемент `<applicationInitialization>` с параметром `doAppInitAfterRestart`, для которого установлено значение `true`, к элементам `<system.webServer>` в файле *web.config* приложения:</span><span class="sxs-lookup"><span data-stu-id="c302a-721">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="c302a-722">Время ожидания в режиме простоя</span><span class="sxs-lookup"><span data-stu-id="c302a-722">Idle Timeout</span></span>

<span data-ttu-id="c302a-723">*Применяется только к приложениям, размещенным в процессе.*</span><span class="sxs-lookup"><span data-stu-id="c302a-723">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="c302a-724">Чтобы предотвратить переход приложения из состояния простоя, задайте для пула приложений время ожидания в режиме простоя с помощью диспетчера IIS:</span><span class="sxs-lookup"><span data-stu-id="c302a-724">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="c302a-725">Выберите **Пулы приложений** на панели **Подключения**.</span><span class="sxs-lookup"><span data-stu-id="c302a-725">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="c302a-726">Щелкните пул приложений в списке правой кнопкой мыши и выберите **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-726">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="c302a-727">По умолчанию для параметра **Тайм-аут простоя (в минутах)** установлено значение **20** минут.</span><span class="sxs-lookup"><span data-stu-id="c302a-727">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="c302a-728">Задайте для параметра **Тайм-аут простоя (в минутах)** значение **0**.</span><span class="sxs-lookup"><span data-stu-id="c302a-728">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="c302a-729">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c302a-729">Select **OK**.</span></span>
1. <span data-ttu-id="c302a-730">Перезапустите рабочий процесс.</span><span class="sxs-lookup"><span data-stu-id="c302a-730">Recycle the worker process.</span></span>

<span data-ttu-id="c302a-731">Чтобы не истекло время ожидания в приложениях, размещенных [вне процесса](#out-of-process-hosting-model), воспользуйтесь одним из таких методов:</span><span class="sxs-lookup"><span data-stu-id="c302a-731">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="c302a-732">Проверьте связь с приложением из внешней службы, чтобы оно продолжило работу.</span><span class="sxs-lookup"><span data-stu-id="c302a-732">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="c302a-733">Если приложение размещает только фоновые службы, избегайте размещения в службах IIS и воспользуйтесь [службой Windows для размещения приложения ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="c302a-733">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="c302a-734">Дополнительные ресурсы по модулю инициализации приложений и времени ожидания в режиме простоя</span><span class="sxs-lookup"><span data-stu-id="c302a-734">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="c302a-735">Инициализация приложений в IIS 8.0</span><span class="sxs-lookup"><span data-stu-id="c302a-735">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="c302a-736">[Инициализация приложений \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="c302a-736">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="c302a-737">[Параметры модели процессов для пула приложений \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="c302a-737">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="c302a-738">Ресурсы развертывания для администраторов IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-738">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="c302a-739">Документация по службам IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-739">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="c302a-740">Начало работы с диспетчером IIS в IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-740">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="c302a-741">Развертывание приложений .NET Core</span><span class="sxs-lookup"><span data-stu-id="c302a-741">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="c302a-742">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c302a-742">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="c302a-743">Официальный веб-сайт Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-743">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="c302a-744">Библиотека технического содержимого по Windows Server</span><span class="sxs-lookup"><span data-stu-id="c302a-744">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="c302a-745">HTTP/2 в IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-745">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c302a-746">Руководство по публикации приложения ASP.NET Core на сервере IIS см. по этой ссылке: <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="c302a-746">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="c302a-747">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="c302a-747">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="c302a-748">Поддерживаемые операционные системы</span><span class="sxs-lookup"><span data-stu-id="c302a-748">Supported operating systems</span></span>

<span data-ttu-id="c302a-749">Поддерживаются следующие операционные системы:</span><span class="sxs-lookup"><span data-stu-id="c302a-749">The following operating systems are supported:</span></span>

* <span data-ttu-id="c302a-750">Windows 7 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="c302a-750">Windows 7 or later</span></span>
* <span data-ttu-id="c302a-751">Windows Server 2008 R2 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="c302a-751">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c302a-752">[Сервер HTTP.sys](xref:fundamentals/servers/httpsys) (ранее назывался WebListener) не работает в конфигурации обратного прокси-сервера со службами IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-752">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="c302a-753">Используйте [сервер Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="c302a-753">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="c302a-754">Сведения о размещении в Azure см. в статье <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="c302a-754">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="c302a-755">Рекомендации по устранению неполадок см. в статье <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="c302a-755">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="c302a-756">Поддерживаемые платформы</span><span class="sxs-lookup"><span data-stu-id="c302a-756">Supported platforms</span></span>

<span data-ttu-id="c302a-757">Поддерживаются приложения, опубликованные для развертывания в 32-разрядных (x86) или 64-разрядных (x64) системах.</span><span class="sxs-lookup"><span data-stu-id="c302a-757">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="c302a-758">Развертывайте 32-разрядную версию (x86) приложения с использованием 32-разрядного пакета SDK для .NET Core, кроме следующих случаев:</span><span class="sxs-lookup"><span data-stu-id="c302a-758">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="c302a-759">Приложению требуется более широкое адресное пространство виртуальной памяти, доступное в 64-разрядной версии приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-759">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="c302a-760">Приложению требуется стек IIS большего размера.</span><span class="sxs-lookup"><span data-stu-id="c302a-760">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="c302a-761">В коде присутствуют зависимости 64-разрядной версии.</span><span class="sxs-lookup"><span data-stu-id="c302a-761">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="c302a-762">Используйте 64-разрядный (x64) пакет SDK для .NET Core для публикации 64-разрядной версии приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-762">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="c302a-763">Для этого в системе узла должна присутствовать 64-разрядная версия среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="c302a-763">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="c302a-764">Модели размещения</span><span class="sxs-lookup"><span data-stu-id="c302a-764">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="c302a-765">Модель внутрипроцессного размещения</span><span class="sxs-lookup"><span data-stu-id="c302a-765">In-process hosting model</span></span>

<span data-ttu-id="c302a-766">При внутрипроцессном размещении приложение ASP.NET Core выполняется в том же процессе, что и рабочий процесс IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-766">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="c302a-767">Внутрипроцессное размещение обеспечивает более высокую производительность по сравнению с внепроцессным размещением по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="c302a-767">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="c302a-768">Запросы не передаются через адаптер замыкания на себя.</span><span class="sxs-lookup"><span data-stu-id="c302a-768">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="c302a-769">Адаптер замыкания на себя — это сетевой интерфейс, который возвращает исходящий сетевой трафик на тот же компьютер.</span><span class="sxs-lookup"><span data-stu-id="c302a-769">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="c302a-770">IIS обрабатывает управление процессом с помощью [службы активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="c302a-770">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c302a-771">[Модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="c302a-771">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="c302a-772">Инициализирует приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-772">Performs app initialization.</span></span>
  * <span data-ttu-id="c302a-773">Загружает [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="c302a-773">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="c302a-774">Вызывает `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="c302a-774">Calls `Program.Main`.</span></span>
* <span data-ttu-id="c302a-775">Управляет жизненным циклом собственного запроса IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-775">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="c302a-776">Модель внутрипроцессного размещения не поддерживается для приложений ASP.NET Core, предназначенных для .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="c302a-776">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="c302a-777">На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением, размещенным внутри процесса.</span><span class="sxs-lookup"><span data-stu-id="c302a-777">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Модуль ASP.NET Core в сценарии внутрипроцессного размещения](index/_static/ancm-inprocess.png)

<span data-ttu-id="c302a-779">Запрос поступает из Интернета в драйвер HTTP.sys в режиме ядра.</span><span class="sxs-lookup"><span data-stu-id="c302a-779">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="c302a-780">Драйвер направляет собственный запрос к IIS на настроенный порт веб-сайта — обычно 80 (HTTP) или 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c302a-780">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="c302a-781">Модуль ASP.NET Core получает собственный запрос и передает его на HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="c302a-781">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="c302a-782">HTTP-сервер IIS — это реализация внутрипроцессного сервера для IIS, в которой запрос преобразовывается из собственной формы в управляемую.</span><span class="sxs-lookup"><span data-stu-id="c302a-782">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="c302a-783">После того как HTTP-сервер IIS обрабатывает запрос, он передается в конвейер ПО промежуточного слоя ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-783">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="c302a-784">Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-784">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="c302a-785">Ответ приложения передается обратно в службы IIS через HTTP-сервер IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-785">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="c302a-786">IIS отправляет ответ клиенту, который инициировал запрос.</span><span class="sxs-lookup"><span data-stu-id="c302a-786">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="c302a-787">Внутрипроцессное размещение необходимо явно выбирать в существующих приложениях, но в шаблонах [dotnet new](/dotnet/core/tools/dotnet-new) оно включено по умолчанию для всех сценариев IIS и IIS Express.</span><span class="sxs-lookup"><span data-stu-id="c302a-787">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="c302a-788">`CreateDefaultBuilder` добавляет экземпляр <xref:Microsoft.AspNetCore.Hosting.Server.IServer>. При этом вызывается метод <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> для загрузки [CoreCLR](/dotnet/standard/glossary#coreclr) и размещения приложения внутри рабочего процесса IIS (*w3wp.exe* или *iisexpress.exe*).</span><span class="sxs-lookup"><span data-stu-id="c302a-788">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="c302a-789">Тесты производительности показывают, что размещение приложения .NET Core внутри процесса позволяет обрабатывать значительно больше запросов, чем при размещении приложения вне процесса с перенаправлением запросов к серверу [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="c302a-789">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="c302a-790">Модель размещения вне процесса</span><span class="sxs-lookup"><span data-stu-id="c302a-790">Out-of-process hosting model</span></span>

<span data-ttu-id="c302a-791">Так как приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, модуль ASP.NET Core обрабатывает управление процессами.</span><span class="sxs-lookup"><span data-stu-id="c302a-791">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="c302a-792">Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="c302a-792">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="c302a-793">Это, по сути, совпадает с поведением приложений, выполняемых внутрипроцессно и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="c302a-793">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c302a-794">На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением, размещенным вне процесса.</span><span class="sxs-lookup"><span data-stu-id="c302a-794">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Модуль ASP.NET Core в сценарии внепроцессного размещения](index/_static/ancm-outofprocess.png)

<span data-ttu-id="c302a-796">Запросы поступают из Интернета в драйвер HTTP.sys в режиме ядра.</span><span class="sxs-lookup"><span data-stu-id="c302a-796">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="c302a-797">Драйвер направляет запросы к службам IIS на настроенный порт веб-сайта — обычно 80 (HTTP) или 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c302a-797">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="c302a-798">Модуль перенаправляет запросы Kestrel на случайный порт для приложения, отличающийся от порта 80 или 443.</span><span class="sxs-lookup"><span data-stu-id="c302a-798">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="c302a-799">Модуль задает порт с помощью переменной среды во время запуска, а расширение <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> настраивает сервер для прослушивания `http://localhost:{PORT}`.</span><span class="sxs-lookup"><span data-stu-id="c302a-799">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="c302a-800">Выполняются дополнительные проверки, и запросы не из модуля отклоняются.</span><span class="sxs-lookup"><span data-stu-id="c302a-800">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="c302a-801">Модуль не поддерживает переадресацию по HTTPS, поэтому запросы переадресовываются по протоколу HTTP, даже если были получены IIS по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c302a-801">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="c302a-802">После того как Kestrel забирает запрос из модуля, запрос передается в конвейер ПО промежуточного слоя ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-802">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="c302a-803">Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-803">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="c302a-804">ПО промежуточного слоя, добавленное интеграцией IIS, обновляет схему, удаленный IP-адрес и базовый путь для переадресации запроса в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c302a-804">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="c302a-805">Отклик приложения передается обратно в службу IIS, которая отправляет его обратно в HTTP-клиент, инициировавший запрос.</span><span class="sxs-lookup"><span data-stu-id="c302a-805">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="c302a-806">Инструкции по настройке модуля ASP.NET Core см. в статье <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c302a-806">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="c302a-807">Дополнительные сведения о размещении см. в разделе [Размещение в ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="c302a-807">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="c302a-808">Настройка приложения</span><span class="sxs-lookup"><span data-stu-id="c302a-808">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="c302a-809">Включение компонентов IISIntegration</span><span class="sxs-lookup"><span data-stu-id="c302a-809">Enable the IISIntegration components</span></span>

<span data-ttu-id="c302a-810">При создании узла в `CreateWebHostBuilder` (*Program.cs*) вызовите метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, чтобы включить интеграцию IIS:</span><span class="sxs-lookup"><span data-stu-id="c302a-810">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="c302a-811">Дополнительные сведения о методе `CreateDefaultBuilder` см. в разделе <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="c302a-811">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="c302a-812">Параметры служб IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-812">IIS options</span></span>

<span data-ttu-id="c302a-813">**Модель внутрипроцессного размещения**</span><span class="sxs-lookup"><span data-stu-id="c302a-813">**In-process hosting model**</span></span>

<span data-ttu-id="c302a-814">Чтобы настроить параметры сервера IIS, включите конфигурацию служб для <xref:Microsoft.AspNetCore.Builder.IISServerOptions> в <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="c302a-814">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="c302a-815">В следующем примере показано, как отключить AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="c302a-815">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="c302a-816">Параметр</span><span class="sxs-lookup"><span data-stu-id="c302a-816">Option</span></span>                         | <span data-ttu-id="c302a-817">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c302a-817">Default</span></span> | <span data-ttu-id="c302a-818">Параметр</span><span class="sxs-lookup"><span data-stu-id="c302a-818">Setting</span></span> |
| ---
<span data-ttu-id="c302a-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-820">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-820">'Blazor'</span></span>
- <span data-ttu-id="c302a-821">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-821">'Identity'</span></span>
- <span data-ttu-id="c302a-822">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-822">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-823">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-823">'Razor'</span></span>
- <span data-ttu-id="c302a-824">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-824">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-826">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-826">'Blazor'</span></span>
- <span data-ttu-id="c302a-827">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-827">'Identity'</span></span>
- <span data-ttu-id="c302a-828">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-828">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-829">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-829">'Razor'</span></span>
- <span data-ttu-id="c302a-830">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-830">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-832">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-832">'Blazor'</span></span>
- <span data-ttu-id="c302a-833">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-833">'Identity'</span></span>
- <span data-ttu-id="c302a-834">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-834">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-835">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-835">'Razor'</span></span>
- <span data-ttu-id="c302a-836">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-836">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-838">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-838">'Blazor'</span></span>
- <span data-ttu-id="c302a-839">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-839">'Identity'</span></span>
- <span data-ttu-id="c302a-840">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-840">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-841">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-841">'Razor'</span></span>
- <span data-ttu-id="c302a-842">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-842">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-844">'Blazor'</span></span>
- <span data-ttu-id="c302a-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-845">'Identity'</span></span>
- <span data-ttu-id="c302a-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-847">'Razor'</span></span>
- <span data-ttu-id="c302a-848">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-850">'Blazor'</span></span>
- <span data-ttu-id="c302a-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-851">'Identity'</span></span>
- <span data-ttu-id="c302a-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-853">'Razor'</span></span>
- <span data-ttu-id="c302a-854">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-856">'Blazor'</span></span>
- <span data-ttu-id="c302a-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-857">'Identity'</span></span>
- <span data-ttu-id="c302a-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-859">'Razor'</span></span>
- <span data-ttu-id="c302a-860">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-862">'Blazor'</span></span>
- <span data-ttu-id="c302a-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-863">'Identity'</span></span>
- <span data-ttu-id="c302a-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-865">'Razor'</span></span>
- <span data-ttu-id="c302a-866">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-868">'Blazor'</span></span>
- <span data-ttu-id="c302a-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-869">'Identity'</span></span>
- <span data-ttu-id="c302a-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-871">'Razor'</span></span>
- <span data-ttu-id="c302a-872">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-874">'Blazor'</span></span>
- <span data-ttu-id="c302a-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-875">'Identity'</span></span>
- <span data-ttu-id="c302a-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-877">'Razor'</span></span>
- <span data-ttu-id="c302a-878">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-880">'Blazor'</span></span>
- <span data-ttu-id="c302a-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-881">'Identity'</span></span>
- <span data-ttu-id="c302a-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-883">'Razor'</span></span>
- <span data-ttu-id="c302a-884">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-886">'Blazor'</span></span>
- <span data-ttu-id="c302a-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-887">'Identity'</span></span>
- <span data-ttu-id="c302a-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-889">'Razor'</span></span>
- <span data-ttu-id="c302a-890">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-892">'Blazor'</span></span>
- <span data-ttu-id="c302a-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-893">'Identity'</span></span>
- <span data-ttu-id="c302a-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-895">'Razor'</span></span>
- <span data-ttu-id="c302a-896">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-896">'SignalR' uid:</span></span> 

<span data-ttu-id="c302a-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-898">'Blazor'</span></span>
- <span data-ttu-id="c302a-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-899">'Identity'</span></span>
- <span data-ttu-id="c302a-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-901">'Razor'</span></span>
- <span data-ttu-id="c302a-902">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-902">'SignalR' uid:</span></span> 

<span data-ttu-id="c302a-903">---- | | `AutomaticAuthentication`      | `true`  | Если значение — `true`, сервер IIS задает свойство `HttpContext.User`, использующее [проверку подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-903">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c302a-904">Если значение — `false`, сервер только предоставляет идентификатор для `HttpContext.User` и отвечает на явные запросы защиты от `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="c302a-904">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="c302a-905">Для работы `AutomaticAuthentication` необходимо включить в службах IIS проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="c302a-905">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="c302a-906">Дополнительные сведения: [Проверка подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-906">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c302a-907">| | `AuthenticationDisplayName`    | `null`  | Задает отображаемое имя для пользователей на страницах входа.</span><span class="sxs-lookup"><span data-stu-id="c302a-907">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="c302a-908">**Модель размещения вне процесса**</span><span class="sxs-lookup"><span data-stu-id="c302a-908">**Out-of-process hosting model**</span></span>

<span data-ttu-id="c302a-909">Чтобы настроить параметры IIS, включите конфигурацию служб для <xref:Microsoft.AspNetCore.Builder.IISOptions> в <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="c302a-909">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="c302a-910">В следующем примере приложению запрещается заполнение `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="c302a-910">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="c302a-911">Параметр</span><span class="sxs-lookup"><span data-stu-id="c302a-911">Option</span></span>                         | <span data-ttu-id="c302a-912">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c302a-912">Default</span></span> | <span data-ttu-id="c302a-913">Параметр</span><span class="sxs-lookup"><span data-stu-id="c302a-913">Setting</span></span> |
| ---
<span data-ttu-id="c302a-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-915">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-915">'Blazor'</span></span>
- <span data-ttu-id="c302a-916">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-916">'Identity'</span></span>
- <span data-ttu-id="c302a-917">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-917">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-918">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-918">'Razor'</span></span>
- <span data-ttu-id="c302a-919">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-919">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-921">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-921">'Blazor'</span></span>
- <span data-ttu-id="c302a-922">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-922">'Identity'</span></span>
- <span data-ttu-id="c302a-923">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-923">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-924">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-924">'Razor'</span></span>
- <span data-ttu-id="c302a-925">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-925">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-927">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-927">'Blazor'</span></span>
- <span data-ttu-id="c302a-928">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-928">'Identity'</span></span>
- <span data-ttu-id="c302a-929">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-929">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-930">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-930">'Razor'</span></span>
- <span data-ttu-id="c302a-931">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-931">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-933">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-933">'Blazor'</span></span>
- <span data-ttu-id="c302a-934">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-934">'Identity'</span></span>
- <span data-ttu-id="c302a-935">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-935">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-936">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-936">'Razor'</span></span>
- <span data-ttu-id="c302a-937">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-937">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-939">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-939">'Blazor'</span></span>
- <span data-ttu-id="c302a-940">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-940">'Identity'</span></span>
- <span data-ttu-id="c302a-941">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-941">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-942">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-942">'Razor'</span></span>
- <span data-ttu-id="c302a-943">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-943">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-945">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-945">'Blazor'</span></span>
- <span data-ttu-id="c302a-946">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-946">'Identity'</span></span>
- <span data-ttu-id="c302a-947">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-947">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-948">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-948">'Razor'</span></span>
- <span data-ttu-id="c302a-949">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-949">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-951">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-951">'Blazor'</span></span>
- <span data-ttu-id="c302a-952">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-952">'Identity'</span></span>
- <span data-ttu-id="c302a-953">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-953">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-954">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-954">'Razor'</span></span>
- <span data-ttu-id="c302a-955">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-955">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-957">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-957">'Blazor'</span></span>
- <span data-ttu-id="c302a-958">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-958">'Identity'</span></span>
- <span data-ttu-id="c302a-959">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-959">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-960">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-960">'Razor'</span></span>
- <span data-ttu-id="c302a-961">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-961">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-963">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-963">'Blazor'</span></span>
- <span data-ttu-id="c302a-964">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-964">'Identity'</span></span>
- <span data-ttu-id="c302a-965">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-965">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-966">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-966">'Razor'</span></span>
- <span data-ttu-id="c302a-967">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-967">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-969">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-969">'Blazor'</span></span>
- <span data-ttu-id="c302a-970">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-970">'Identity'</span></span>
- <span data-ttu-id="c302a-971">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-971">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-972">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-972">'Razor'</span></span>
- <span data-ttu-id="c302a-973">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-973">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-975">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-975">'Blazor'</span></span>
- <span data-ttu-id="c302a-976">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-976">'Identity'</span></span>
- <span data-ttu-id="c302a-977">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-977">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-978">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-978">'Razor'</span></span>
- <span data-ttu-id="c302a-979">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-979">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-981">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-981">'Blazor'</span></span>
- <span data-ttu-id="c302a-982">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-982">'Identity'</span></span>
- <span data-ttu-id="c302a-983">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-983">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-984">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-984">'Razor'</span></span>
- <span data-ttu-id="c302a-985">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-985">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-987">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-987">'Blazor'</span></span>
- <span data-ttu-id="c302a-988">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-988">'Identity'</span></span>
- <span data-ttu-id="c302a-989">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-989">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-990">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-990">'Razor'</span></span>
- <span data-ttu-id="c302a-991">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-991">'SignalR' uid:</span></span> 

<span data-ttu-id="c302a-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-993">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-993">'Blazor'</span></span>
- <span data-ttu-id="c302a-994">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-994">'Identity'</span></span>
- <span data-ttu-id="c302a-995">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-995">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-996">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-996">'Razor'</span></span>
- <span data-ttu-id="c302a-997">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-997">'SignalR' uid:</span></span> 

<span data-ttu-id="c302a-998">---- | | `AutomaticAuthentication`      | `true`  | Если значение — `true`, [ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components) задает свойство `HttpContext.User`, которое прошло [проверку подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-998">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c302a-999">Если значение — `false`, ПО промежуточного слоя только предоставляет идентификатор для `HttpContext.User` и отвечает на явные запросы защиты от `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="c302a-999">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="c302a-1000">Для работы `AutomaticAuthentication` необходимо включить в службах IIS проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="c302a-1000">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="c302a-1001">Дополнительные сведения см. в статье о [проверке подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-1001">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="c302a-1002">| | `AuthenticationDisplayName`    | `null`  | Задает отображаемое имя для пользователей на страницах входа.</span><span class="sxs-lookup"><span data-stu-id="c302a-1002">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="c302a-1003">| | `ForwardClientCertificate`     | `true`  | Если значение — `true` и если присутствует заголовок запроса `MS-ASPNETCORE-CLIENTCERT`, происходит заполнение `HttpContext.Connection.ClientCertificate`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1003">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c302a-1004">Сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="c302a-1004">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c302a-1005">[ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components), которое настраивает ПО промежуточного слоя переадресации заголовков, и модуль ASP.NET Core настраиваются на пересылку схемы (HTTP/HTTPS) и удаленного IP-адреса расположения, где был сформирован запрос.</span><span class="sxs-lookup"><span data-stu-id="c302a-1005">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="c302a-1006">Для приложений, размещенных за дополнительными прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="c302a-1006">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="c302a-1007">Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="c302a-1007">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="c302a-1008">Файл web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-1008">web.config file</span></span>

<span data-ttu-id="c302a-1009">В файле *web.config* содержится конфигурация [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c302a-1009">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="c302a-1010">Создание, преобразование и публикация файла *web.config* обрабатываются целевым объектом MSBuild (`_TransformWebConfig`) при публикации проекта.</span><span class="sxs-lookup"><span data-stu-id="c302a-1010">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="c302a-1011">Этот целевой объект присутствует в целевых веб-пакетах SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="c302a-1011">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="c302a-1012">Пакет SDK задается в начале файла проекта:</span><span class="sxs-lookup"><span data-stu-id="c302a-1012">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="c302a-1013">Если в проекте нет файла *web.config*, он создается с соответствующими аргументами *processPath* и *arguments* для настройки модуля ASP.NET Core и переносится в [опубликованные выходные данные](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="c302a-1013">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="c302a-1014">Если в проекте есть файл *web.config*, он преобразуется с соответствующими аргументами *processPath* и *arguments* для настройки модуля ASP.NET Core и переносится в опубликованные выходные данные.</span><span class="sxs-lookup"><span data-stu-id="c302a-1014">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="c302a-1015">Преобразование не изменяет параметры конфигурации служб IIS, включенные в файл.</span><span class="sxs-lookup"><span data-stu-id="c302a-1015">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="c302a-1016">Файл *web.config* может содержать дополнительные параметры конфигурации IIS, управляющие активными модулями IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1016">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="c302a-1017">Сведения о модулях IIS, которые могут обрабатывать запросы к приложениям ASP.NET Core, см. в статье [Модули IIS](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="c302a-1017">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="c302a-1018">Чтобы пакет SDK не преобразовывал файл *web.config*, добавьте в файл проекта свойство **\<IsTransformWebConfigDisabled>** :</span><span class="sxs-lookup"><span data-stu-id="c302a-1018">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="c302a-1019">Если пакет SDK не преобразует файл, аргументы *processPath* и *arguments* нужно задать вручную.</span><span class="sxs-lookup"><span data-stu-id="c302a-1019">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="c302a-1020">Для получения дополнительной информации см. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1020">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="c302a-1021">Расположение файла web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-1021">web.config file location</span></span>

<span data-ttu-id="c302a-1022">Для корректной настройки [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module) необходимо наличие файла *web.config* в [корневой папке содержимого](xref:fundamentals/index#content-root) развертываемого приложения (как правило, это основной путь приложения).</span><span class="sxs-lookup"><span data-stu-id="c302a-1022">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="c302a-1023">Это расположение соответствует физическому пути веб-сайта, указанному в службах IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1023">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="c302a-1024">Файл *web.config* требуется в корне приложения, чтобы разрешить публикацию нескольких приложений с помощью веб-развертывания.</span><span class="sxs-lookup"><span data-stu-id="c302a-1024">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="c302a-1025">По физическому пути приложения находятся файлы с конфиденциальной информацией: *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (комментарии к XML-документации) и *\<assembly>.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="c302a-1025">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="c302a-1026">Когда файл *web.config* присутствует и сайт запускается нормально, службы IIS не обрабатывают запросы к этим файлам.</span><span class="sxs-lookup"><span data-stu-id="c302a-1026">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="c302a-1027">Если файл *web.config* отсутствует, неправильно именован или не может настроить нормальный запуск сайта, службы IIS могут свободно передавать содержимое этих конфиденциальных файлов.</span><span class="sxs-lookup"><span data-stu-id="c302a-1027">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="c302a-1028">**Файл *web.config* должен постоянно находиться в развертывании, у этого файла должно быть правильное имя, и файл должен быть в состоянии настроить нормальный запуск сайта. Никогда не удаляйте файл *web.config* из развертывания в рабочей среде.**</span><span class="sxs-lookup"><span data-stu-id="c302a-1028">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="c302a-1029">Преобразование web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-1029">Transform web.config</span></span>

<span data-ttu-id="c302a-1030">Если вам нужно преобразовать *web.config* при публикации (например, задать переменные среды на основе конфигурации, профиля или среды), см. раздел <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1030">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="c302a-1031">Конфигурация IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1031">IIS configuration</span></span>

<span data-ttu-id="c302a-1032">**Операционные системы семейства Windows Server**</span><span class="sxs-lookup"><span data-stu-id="c302a-1032">**Windows Server operating systems**</span></span>

<span data-ttu-id="c302a-1033">Включите роль сервера **Веб-сервер (IIS)** и настройте службы роли.</span><span class="sxs-lookup"><span data-stu-id="c302a-1033">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="c302a-1034">В меню **Управление** запустите мастер **Добавить роли и компоненты** или в окне **Диспетчер серверов** щелкните соответствующую ссылку.</span><span class="sxs-lookup"><span data-stu-id="c302a-1034">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="c302a-1035">На этапе **Роли сервера** установите флажок **Веб-сервер (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="c302a-1035">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Роль "Веб-сервер (IIS)" выбрана на странице "Выбор ролей сервера".](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="c302a-1037">После этапа выбора **компонентов** запускается этап выбора **служб роли** для веб-сервера (IIS).</span><span class="sxs-lookup"><span data-stu-id="c302a-1037">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="c302a-1038">Выберите нужные службы роли IIS или оставьте службы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c302a-1038">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Службы роли по умолчанию, выбранные на странице "Выбор служб ролей".](index/_static/role-services-ws2016.png)

   <span data-ttu-id="c302a-1040">**Проверка подлинности Windows (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c302a-1040">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c302a-1041">Чтобы включить проверку подлинности Windows, разверните такие узлы: **Веб-сервер** > **Безопасность**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1041">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="c302a-1042">Выберите компонент **Проверка подлинности Windows**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1042">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c302a-1043">Дополнительные сведения см. в статьях [Проверка подлинности Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) и [Настройка проверки подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-1043">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c302a-1044">**Протокол WebSocket (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c302a-1044">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c302a-1045">Протокол WebSocket поддерживается в ASP.NET Core начиная с версии 1.1.</span><span class="sxs-lookup"><span data-stu-id="c302a-1045">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c302a-1046">Чтобы включить протокол WebSocket, разверните такие узлы: **Веб-сервер** > **Разработка приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1046">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="c302a-1047">Выберите компонент **Протокол WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1047">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c302a-1048">Дополнительные сведения см. в разделе [Протокол WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="c302a-1048">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c302a-1049">Пройдите этап **Подтверждение**, чтобы установить роль и службы веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="c302a-1049">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="c302a-1050">После установки роли **Веб-сервер (IIS)** перезагружать сервер или службы IIS не требуется.</span><span class="sxs-lookup"><span data-stu-id="c302a-1050">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="c302a-1051">**Операционные системы Windows для настольных компьютеров**</span><span class="sxs-lookup"><span data-stu-id="c302a-1051">**Windows desktop operating systems**</span></span>

<span data-ttu-id="c302a-1052">Включите компоненты **Консоль управления IIS** и **Службы Интернета**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1052">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c302a-1053">Последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).</span><span class="sxs-lookup"><span data-stu-id="c302a-1053">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="c302a-1054">Разверните узел **Службы IIS**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1054">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="c302a-1055">Разверните узел **Средства управления веб-сайтом**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1055">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="c302a-1056">Установите флажок **Консоль управления IIS**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1056">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="c302a-1057">Установите флажок **Службы Интернета**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1057">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c302a-1058">В группе **Службы Интернета** оставьте компоненты по умолчанию или настройте компоненты IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1058">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="c302a-1059">**Проверка подлинности Windows (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c302a-1059">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c302a-1060">Чтобы включить проверку подлинности Windows, разверните такие узлы: **Службы Интернета** > **Безопасность**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1060">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="c302a-1061">Выберите компонент **Проверка подлинности Windows**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1061">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c302a-1062">Дополнительные сведения см. в статьях [Проверка подлинности Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) и [Настройка проверки подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-1062">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c302a-1063">**Протокол WebSocket (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c302a-1063">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c302a-1064">Протокол WebSocket поддерживается в ASP.NET Core начиная с версии 1.1.</span><span class="sxs-lookup"><span data-stu-id="c302a-1064">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c302a-1065">Чтобы включить протокол WebSocket, разверните такие узлы: **Службы Интернета** > **Компоненты разработки приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1065">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="c302a-1066">Выберите компонент **Протокол WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1066">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c302a-1067">Дополнительные сведения см. в разделе [Протокол WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="c302a-1067">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c302a-1068">Если во время установки IIS потребуется перезагрузка, перезагрузите систему.</span><span class="sxs-lookup"><span data-stu-id="c302a-1068">If the IIS installation requires a restart, restart the system.</span></span>

![Группы "Консоль управления IIS" и "Службы Интернета" выбраны в окне "Компоненты Windows".](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="c302a-1070">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="c302a-1070">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="c302a-1071">Установите *пакет размещения .NET Core* в размещающей системе.</span><span class="sxs-lookup"><span data-stu-id="c302a-1071">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="c302a-1072">В составе пакета устанавливаются среда выполнения .NET Core, библиотека .NET Core и [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c302a-1072">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="c302a-1073">Модуль позволяет запускать приложения ASP.NET Core под управлением IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1073">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c302a-1074">Если пакет размещения устанавливается до установки служб IIS, его нужно восстановить.</span><span class="sxs-lookup"><span data-stu-id="c302a-1074">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="c302a-1075">После установки служб IIS запустите установщик пакета размещения еще раз.</span><span class="sxs-lookup"><span data-stu-id="c302a-1075">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="c302a-1076">Если пакет размещения устанавливается после установки 64-разрядной (x 64) версии .NET Core, пакеты SDK могут не отображаться (см. раздел [Пакеты SDK .NET Core не обнаружены](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="c302a-1076">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="c302a-1077">Сведения об устранении проблемы см. в статье <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1077">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="c302a-1078">Скачать</span><span class="sxs-lookup"><span data-stu-id="c302a-1078">Download</span></span>

1. <span data-ttu-id="c302a-1079">Перейдите на страницу [загрузки .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="c302a-1079">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="c302a-1080">Выберите требуемую версию .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1080">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="c302a-1081">В столбце **Запуск приложений — среда выполнения** найдите строку, содержащую нужную версию среды выполнения .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1081">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="c302a-1082">Скачайте установщик по ссылке **Hosting Bundle** (Пакет размещения).</span><span class="sxs-lookup"><span data-stu-id="c302a-1082">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="c302a-1083">Некоторые установщики содержат версии выпусков, которые достигли конца своего жизненного цикла и больше не поддерживаются корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="c302a-1083">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="c302a-1084">Дополнительные сведения см. в разделе [Политика поддержки](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="c302a-1084">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="c302a-1085">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="c302a-1085">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="c302a-1086">Запустите установщик на сервере.</span><span class="sxs-lookup"><span data-stu-id="c302a-1086">Run the installer on the server.</span></span> <span data-ttu-id="c302a-1087">При запуске установщика из командной оболочки администратора доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="c302a-1087">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="c302a-1088">`OPT_NO_ANCM=1`. Пропуск установки модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1088">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="c302a-1089">`OPT_NO_RUNTIME=1`. Пропуск установки среды выполнения .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1089">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="c302a-1090">Используется, когда на сервере размещаются только [автономные развертывания](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="c302a-1090">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="c302a-1091">`OPT_NO_SHAREDFX=1`. Пропуск установки общей платформы ASP.NET (среды выполнения ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="c302a-1091">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="c302a-1092">Используется, когда на сервере размещаются только [автономные развертывания](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="c302a-1092">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="c302a-1093">`OPT_NO_X86=1`. Пропуск установки сред выполнения x86.</span><span class="sxs-lookup"><span data-stu-id="c302a-1093">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="c302a-1094">Этот параметр следует использовать, если вы наверняка не будете размещать 32-разрядные приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1094">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="c302a-1095">Если есть хоть малейшая возможность, что в будущем придется размещать и 32-разрядные, и 64-разрядные приложения, не указывайте этот параметр и установите обе среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1095">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="c302a-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`. Отключение проверки использования общей конфигурации IIS, если файл общей конфигурации (*applicationHost.config*) находится на том же компьютере, что и установка IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="c302a-1097">*Доступен только для пакетных установщиков размещения ASP.NET Core 2.2 или более поздней версии.*</span><span class="sxs-lookup"><span data-stu-id="c302a-1097">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="c302a-1098">Для получения дополнительной информации см. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1098">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="c302a-1099">Перезапустите систему или выполните в командной оболочке следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c302a-1099">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="c302a-1100">Перезапуск служб IIS позволит обнаружить внесенные установщиком изменения в системном пути, который является переменной среды.</span><span class="sxs-lookup"><span data-stu-id="c302a-1100">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="c302a-1101">При установке пакета размещения останавливать отдельные сайты служб IIS вручную не нужно.</span><span class="sxs-lookup"><span data-stu-id="c302a-1101">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="c302a-1102">При перезапуске служб IIS осуществляется перезапуск размещенных приложений (сайтов IIS).</span><span class="sxs-lookup"><span data-stu-id="c302a-1102">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="c302a-1103">Приложения запускаются снова при получении первого запроса, в частности от [модуля инициализации приложений](#application-initialization-module-and-idle-timeout).</span><span class="sxs-lookup"><span data-stu-id="c302a-1103">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="c302a-1104">ASP.NET Core наследует поведение наката для выпусков исправлений пакетов общей платформы.</span><span class="sxs-lookup"><span data-stu-id="c302a-1104">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="c302a-1105">Когда приложения, размещенные в службах IIS, перезапускаются вместе с IIS, они загружаются с последними выпусками исправлений связанных пакетов при получении первого запроса.</span><span class="sxs-lookup"><span data-stu-id="c302a-1105">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="c302a-1106">Если службы IIS не перезапускаются, приложения перезапускаются и демонстрируют поведение наката, когда их рабочие процессы перезапускается и они получают первый запрос.</span><span class="sxs-lookup"><span data-stu-id="c302a-1106">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="c302a-1107">Сведения об общей конфигурации IIS см. в разделе [Модуль ASP.NET Core с общей конфигурацией IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="c302a-1107">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="c302a-1108">Установка веб-развертывания при публикации с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c302a-1108">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="c302a-1109">При развертывании приложений на серверах с помощью [веб-развертывания](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) установите на сервере последнюю версию веб-развертывания.</span><span class="sxs-lookup"><span data-stu-id="c302a-1109">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="c302a-1110">Чтобы установить веб-развертывание, можно использовать [установщик веб-платформы (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) или получить установщик непосредственно в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="c302a-1110">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="c302a-1111">Предпочтительный метод — использовать WebPI.</span><span class="sxs-lookup"><span data-stu-id="c302a-1111">The preferred method is to use WebPI.</span></span> <span data-ttu-id="c302a-1112">WebPI обеспечивает автономную установку и настройку поставщиков размещения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1112">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="c302a-1113">Создание сайта IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1113">Create the IIS site</span></span>

1. <span data-ttu-id="c302a-1114">В размещающей системе создайте папку, в которой будут храниться файлы и папки опубликованного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1114">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="c302a-1115">На следующем этапе путь к папке предоставляется IIS как физический путь к приложению.</span><span class="sxs-lookup"><span data-stu-id="c302a-1115">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="c302a-1116">Дополнительные сведения о папке развертывания и структуре файлов приложения см. в статье <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1116">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="c302a-1117">В окне диспетчера IIS на панели **Подключения** разверните узел сервера.</span><span class="sxs-lookup"><span data-stu-id="c302a-1117">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="c302a-1118">Щелкните правой кнопкой мыши папку **Сайты**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1118">Right-click the **Sites** folder.</span></span> <span data-ttu-id="c302a-1119">В контекстном меню выберите пункт **Добавить веб-сайт**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1119">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="c302a-1120">Укажите имя в поле **Имя сайта** и задайте значение в поле **Физический путь** для созданной папки развертывания приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1120">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="c302a-1121">Укажите конфигурацию **привязки** и нажмите кнопку **ОК**, чтобы создать веб-сайт.</span><span class="sxs-lookup"><span data-stu-id="c302a-1121">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![В окне "Добавить веб-сайт" укажите имя сайта, физический путь и имя узла.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="c302a-1123">**Не используйте** привязки с подстановочными знаками (`http://*:80/` и `http://+:80`) на верхнем уровне.</span><span class="sxs-lookup"><span data-stu-id="c302a-1123">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="c302a-1124">Это может создать уязвимость и поставить ваше приложение под угрозу.</span><span class="sxs-lookup"><span data-stu-id="c302a-1124">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="c302a-1125">Сюда относятся и строгие, и нестрогие подстановочные знаки.</span><span class="sxs-lookup"><span data-stu-id="c302a-1125">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="c302a-1126">Вместо этого используйте имена узлов в явном виде.</span><span class="sxs-lookup"><span data-stu-id="c302a-1126">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="c302a-1127">Привязки с подстановочными знаками на уровне дочерних доменов (например `*.mysub.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость).</span><span class="sxs-lookup"><span data-stu-id="c302a-1127">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="c302a-1128">Дополнительные сведения см. в документе [rfc7230, раздел 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="c302a-1128">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="c302a-1129">Разверните узел сервера и выберите **Пулы приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1129">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="c302a-1130">Щелкните правой кнопкой мыши пул приложений сайта и в контекстном меню выберите пункт **Основные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1130">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="c302a-1131">В окне **Изменение пула приложений** задайте для параметра **Версия среды CLR .NET** значение **Без управляемого кода**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1131">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Выбор значения "Без управляемого кода" для параметра "Версия среды CLR .NET".](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="c302a-1133">ASP.NET Core выполняется в отдельном процессе и управляет средой выполнения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1133">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="c302a-1134">ASP.NET Core не зависит от загрузки среды CLR для ПК (.NET CLR) &mdash; для размещения приложения в рабочем процессе запускается CoreCLR для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1134">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="c302a-1135">Задавать для параметра **Версия среды CLR .NET** значение **Без управляемого кода** необязательно, но рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="c302a-1135">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="c302a-1136">*ASP.NET Core 2.2 или более поздней версии*: для 64-разрядного (x64) [автономного развертывания](/dotnet/core/deploying/#self-contained-deployments-scd), в котором используется [модель размещения в процессе](#in-process-hosting-model), отключите пул приложений для 32-разрядных (x86) процессов.</span><span class="sxs-lookup"><span data-stu-id="c302a-1136">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="c302a-1137">На боковой панели **Действия** в разделе **Пулы приложений** диспетчера IIS выберите **Задать значения по умолчанию для пула приложений** или **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1137">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="c302a-1138">Найдите пункт **Включить 32-разрядные приложения** и задайте значение `False`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1138">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="c302a-1139">Этот параметр не влияет на приложения, развернутые для [размещения вне процесса](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="c302a-1139">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="c302a-1140">Убедитесь в том, что удостоверение модели процесса имеет соответствующие разрешения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1140">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="c302a-1141">При изменении удостоверения по умолчанию для пула приложений (**Модель процесса** >  **Identity** ) с **ApplicationPoolIdentity** на другое, убедитесь, что у нового удостоверения есть соответствующие разрешения для доступа к папке приложения, базе данных и другим необходимым ресурсам.</span><span class="sxs-lookup"><span data-stu-id="c302a-1141">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="c302a-1142">Например, пулу приложений требуются права на чтение и запись в папках, в которых приложение считывает и записывает файлы.</span><span class="sxs-lookup"><span data-stu-id="c302a-1142">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="c302a-1143">**Настройка проверки подлинности Windows (необязательный этап)**</span><span class="sxs-lookup"><span data-stu-id="c302a-1143">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="c302a-1144">См. статью [Configure Windows authentication in an ASP.NET Core app](xref:security/authentication/windowsauth) (Настройка проверки подлинности Windows в приложении ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="c302a-1144">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="c302a-1145">Развертывание приложения</span><span class="sxs-lookup"><span data-stu-id="c302a-1145">Deploy the app</span></span>

<span data-ttu-id="c302a-1146">Разверните приложение в папке IIS, **физический путь** к которой вы указали в рамках раздела [Создание сайта IIS](#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="c302a-1146">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="c302a-1147">Рекомендуется выполнять [веб-развертывание](/iis/publish/using-web-deploy/introduction-to-web-deploy), но есть несколько других способов переместить приложение из папки *публикации* проекта в папку развертывания размещающей системы.</span><span class="sxs-lookup"><span data-stu-id="c302a-1147">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="c302a-1148">Веб-развертывание с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c302a-1148">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="c302a-1149">Сведения о создании профиля публикации для веб-развертывания см. в статье [Профили публикации Visual Studio для развертывания приложений ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="c302a-1149">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="c302a-1150">Если поставщик услуг размещения предоставляет профиль публикации или позволяет его создать, скачайте этот профиль и импортируйте его с помощью диалогового окна **Публикация** в Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c302a-1150">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Диалоговое окно "Публикация"](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="c302a-1152">Веб-развертывание вне Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c302a-1152">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="c302a-1153">[Веб-развертывание](/iis/publish/using-web-deploy/introduction-to-web-deploy) можно также использовать вне Visual Studio с помощью командной строки.</span><span class="sxs-lookup"><span data-stu-id="c302a-1153">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="c302a-1154">Дополнительные сведения см. в статье [Средство веб-развертывания](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="c302a-1154">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="c302a-1155">Альтернативы веб-развертыванию</span><span class="sxs-lookup"><span data-stu-id="c302a-1155">Alternatives to Web Deploy</span></span>

<span data-ttu-id="c302a-1156">Переместить приложение в размещающую систему можно несколькими способами: с помощью копирования вручную, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) или [PowerShell](/powershell/).</span><span class="sxs-lookup"><span data-stu-id="c302a-1156">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="c302a-1157">Дополнительные сведения о развертывании ASP.NET Core в службах IIS см. в разделе [Ресурсы развертывания для администраторов IIS](#deployment-resources-for-iis-administrators).</span><span class="sxs-lookup"><span data-stu-id="c302a-1157">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="c302a-1158">Обзор веб-сайта</span><span class="sxs-lookup"><span data-stu-id="c302a-1158">Browse the website</span></span>

<span data-ttu-id="c302a-1159">Когда приложение будет развернуто в размещающей системе, выполните запрос к одной из общедоступных конечных точек приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1159">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="c302a-1160">В приведенном ниже примере сайт привязан к **имени узла** IIS `www.mysite.com` в **порте** `80`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1160">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="c302a-1161">Запрос отправляется по адресу `http://www.mysite.com`:</span><span class="sxs-lookup"><span data-stu-id="c302a-1161">A request is made to `http://www.mysite.com`:</span></span>

![Начальная страница IIS, загруженная в браузере Microsoft Edge.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="c302a-1163">Заблокированные файлы развертывания</span><span class="sxs-lookup"><span data-stu-id="c302a-1163">Locked deployment files</span></span>

<span data-ttu-id="c302a-1164">Во время выполнения приложения файлы в папке развертывания блокируются.</span><span class="sxs-lookup"><span data-stu-id="c302a-1164">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="c302a-1165">Заблокированные файлы невозможно перезаписать во время развертывания.</span><span class="sxs-lookup"><span data-stu-id="c302a-1165">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="c302a-1166">Чтобы снять блокировку с файлов в развертывании, остановите пул приложений с помощью **одного** из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="c302a-1166">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="c302a-1167">Запустите веб-развертывание и добавьте ссылку на `Microsoft.NET.Sdk.Web` в файл проекта.</span><span class="sxs-lookup"><span data-stu-id="c302a-1167">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="c302a-1168">Файл *app_offline.htm* помещается в корень каталога веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1168">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="c302a-1169">Если файл присутствует, модуль ASP.NET Core корректно завершает работу приложения и обслуживает файл *app_offline.htm* во время развертывания.</span><span class="sxs-lookup"><span data-stu-id="c302a-1169">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="c302a-1170">Дополнительные сведения см. в разделе [Справочник по конфигурации модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="c302a-1170">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="c302a-1171">Вручную остановите пул приложений в диспетчере служб IIS на сервере.</span><span class="sxs-lookup"><span data-stu-id="c302a-1171">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="c302a-1172">С помощью PowerShell удалите *app_offline.htm* (требуется PowerShell 5 или более поздняя версия):</span><span class="sxs-lookup"><span data-stu-id="c302a-1172">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="c302a-1173">Защита данных</span><span class="sxs-lookup"><span data-stu-id="c302a-1173">Data protection</span></span>

<span data-ttu-id="c302a-1174">[Стек защиты данных ASP.NET Core](xref:security/data-protection/introduction) используют несколько [ПО промежуточного слоя](xref:fundamentals/middleware/index) ASP.NET Core, включая ПО, которое применяется для аутентификации.</span><span class="sxs-lookup"><span data-stu-id="c302a-1174">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="c302a-1175">Даже если API-интерфейсы защиты данных не вызываются из пользовательского кода, защиту данных следует настроить для создания постоянного [хранилища криптографических ключей](xref:security/data-protection/implementation/key-management). Это можно сделать с помощью скрипта развертывания или в пользовательском коде.</span><span class="sxs-lookup"><span data-stu-id="c302a-1175">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="c302a-1176">Если защита данных не настроена, ключи хранятся в памяти и удаляются при перезапуске приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1176">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="c302a-1177">Если набор ключей хранится в памяти, при перезапуске приложения происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="c302a-1177">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="c302a-1178">Все токены аутентификации, использующие файлы cookie, становятся недействительными.</span><span class="sxs-lookup"><span data-stu-id="c302a-1178">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="c302a-1179">При выполнении следующего запроса пользователю требуется выполнить вход снова.</span><span class="sxs-lookup"><span data-stu-id="c302a-1179">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="c302a-1180">Все данные, защищенные с помощью набора ключей, больше не могут быть расшифрованы.</span><span class="sxs-lookup"><span data-stu-id="c302a-1180">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="c302a-1181">Это могут быть [токены CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) и [файлы cookie временных данных ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="c302a-1181">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="c302a-1182">Чтобы настроить защиту данных в службах IIS для хранения набора ключей, воспользуйтесь **одним** из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="c302a-1182">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="c302a-1183">**Создание раздела реестра для защиты данных**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1183">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="c302a-1184">Ключи защиты данных, используемые приложениями ASP.NET Core, хранятся во внешнем для приложений реестре.</span><span class="sxs-lookup"><span data-stu-id="c302a-1184">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="c302a-1185">Чтобы хранить эти ключи для определенного приложения, нужно создать разделы реестра для пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-1185">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="c302a-1186">При автономной установке служб IIS, не поддерживающей веб-ферму, можно выполнить [скрипт PowerShell Provision-AutoGenKeys.ps1 для защиты данных](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) применительно к каждому пулу приложений, в который входит приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1186">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="c302a-1187">Этот скрипт создает раздел в реестре HKLM, который будет доступен только для учетной записи рабочего процесса пула приложений, к которому относится соответствующее приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-1187">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="c302a-1188">Неактивные ключи шифруются с помощью API защиты данных с ключом компьютера.</span><span class="sxs-lookup"><span data-stu-id="c302a-1188">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="c302a-1189">В случаях, когда используется веб-ферма, в приложении можно настроить UNC-путь, по которому это приложение будет хранить набор ключей для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="c302a-1189">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="c302a-1190">По умолчанию ключи защиты данных не шифруются.</span><span class="sxs-lookup"><span data-stu-id="c302a-1190">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="c302a-1191">Разрешения на доступ к файлам в сетевой папке должны быть предоставлены только учетной записи Windows, с помощью которой выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-1191">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="c302a-1192">Для защиты неактивных ключей можно использовать сертификат X509.</span><span class="sxs-lookup"><span data-stu-id="c302a-1192">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="c302a-1193">Рассмотрите возможность реализации механизма, позволяющего пользователям отправлять сертификаты: поместите сертификаты в хранилище доверенных сертификатов пользователя и обеспечьте их доступность на всех компьютерах, где выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-1193">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="c302a-1194">Дополнительные сведения см. в разделе [Настройка защиты данных в ASP.NET Core](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="c302a-1194">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="c302a-1195">**Настройка пула приложений IIS для загрузки профиля пользователя**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1195">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="c302a-1196">Этот параметр находится на странице **Дополнительные параметры** пула приложений в разделе **Модель процесса**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1196">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="c302a-1197">Задайте для параметра **Загрузить профиль пользователя** значение `True`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1197">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="c302a-1198">Если задать значение `True`, ключи будут храниться в каталоге профиля пользователя и защищаться с помощью API защиты данных и ключа на уровне учетной записи пользователя.</span><span class="sxs-lookup"><span data-stu-id="c302a-1198">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="c302a-1199">Ключи хранятся в папке *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="c302a-1199">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="c302a-1200">Также необходимо включить атрибут [setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-1200">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="c302a-1201">Значением свойства `setProfileEnvironment` по умолчанию является `true`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1201">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="c302a-1202">В некоторых сценариях (например, в ОС Windows) для параметра `setProfileEnvironment` установлено значение `false`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1202">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="c302a-1203">Если ключи не хранятся в каталоге профиля пользователя:</span><span class="sxs-lookup"><span data-stu-id="c302a-1203">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="c302a-1204">Перейдите к папке *%windir%/system32/inetsrv/config*.</span><span class="sxs-lookup"><span data-stu-id="c302a-1204">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="c302a-1205">Откройте файл *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="c302a-1205">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="c302a-1206">Найдите элемент `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .</span><span class="sxs-lookup"><span data-stu-id="c302a-1206">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="c302a-1207">Убедитесь, что атрибут `setProfileEnvironment` отсутствует и установлено значение по умолчанию `true`, или же явно задайте для атрибута значение `true`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1207">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="c302a-1208">**Использование файловой системы в качестве хранилища набора ключей**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1208">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="c302a-1209">Измените код приложения так, чтобы [в качестве хранилища набора ключей использовалась файловая система](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="c302a-1209">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="c302a-1210">Для защиты набора ключей используйте доверенный сертификат X509.</span><span class="sxs-lookup"><span data-stu-id="c302a-1210">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="c302a-1211">Если сертификат — самозаверяющий, поместите его в доверенное корневое хранилище.</span><span class="sxs-lookup"><span data-stu-id="c302a-1211">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="c302a-1212">При использовании служб IIS в веб-ферме:</span><span class="sxs-lookup"><span data-stu-id="c302a-1212">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="c302a-1213">используйте общую папку, которая доступна всем компьютерам;</span><span class="sxs-lookup"><span data-stu-id="c302a-1213">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="c302a-1214">разверните сертификат X509 на каждом компьютере;</span><span class="sxs-lookup"><span data-stu-id="c302a-1214">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="c302a-1215">настройте [защиту данных в коде](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="c302a-1215">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="c302a-1216">**Настройка политики защиты данных на уровне компьютера**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1216">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="c302a-1217">Система защиты данных обеспечивает ограниченную поддержку задания [политики по умолчанию на уровне компьютера](xref:security/data-protection/configuration/machine-wide-policy) для всех приложений, использующих интерфейсы API защиты данных.</span><span class="sxs-lookup"><span data-stu-id="c302a-1217">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="c302a-1218">Для получения дополнительной информации см. <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1218">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="c302a-1219">Виртуальные каталоги</span><span class="sxs-lookup"><span data-stu-id="c302a-1219">Virtual Directories</span></span>

<span data-ttu-id="c302a-1220">[Виртуальные каталоги IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) не поддерживаются в приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1220">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="c302a-1221">Приложение может размещаться как [вложенное](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="c302a-1221">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="c302a-1222">Вложенные приложения</span><span class="sxs-lookup"><span data-stu-id="c302a-1222">Sub-applications</span></span>

<span data-ttu-id="c302a-1223">Приложение ASP.NET Core можно разместить как [вложенное приложение IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="c302a-1223">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="c302a-1224">Путь к вложенному приложению становится частью URL-адреса главного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1224">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="c302a-1225">В ссылках на статический ресурс во вложенном приложении следует использовать нотацию `~/`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1225">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="c302a-1226">Такая нотация активирует [вспомогательную функцию тега](xref:mvc/views/tag-helpers/intro) для добавления свойства pathbase вложенного приложения в начало отображаемой относительной ссылки.</span><span class="sxs-lookup"><span data-stu-id="c302a-1226">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="c302a-1227">Для вложенного приложения с путем `/subapp_path` ссылка на изображение `src="~/image.png"` отображается как `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1227">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="c302a-1228">ПО промежуточного слоя для обработки статических файлов в главном приложении не обрабатывает такой запрос статического файла.</span><span class="sxs-lookup"><span data-stu-id="c302a-1228">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="c302a-1229">Запрос обрабатывается соответствующим ПО вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1229">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="c302a-1230">Если атрибуту `src` статического ресурса присваивается абсолютный путь (например, `src="/image.png"`), ссылка отображается без свойства pathbase вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1230">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="c302a-1231">ПО промежуточного слоя для обработки статических файлов в главном приложении пытается найти ресурс в [корневом веб-каталоге](xref:fundamentals/index#web-root) главного приложения, что приводит к ошибке *404 — Not Found* (не найдено), кроме случаев, когда статический ресурс доступен из главного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1231">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="c302a-1232">Для размещения приложения ASP.NET Core в качестве приложения, вложенного в другое приложение ASP.NET Core, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="c302a-1232">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="c302a-1233">Создайте пул приложений для вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1233">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="c302a-1234">Установите для параметра **Версия среды CLR .NET** значение **Без управляемого кода**, так как для размещения приложения в рабочем процессе запускается CoreCLR для .NET Core, а не среда CRL для настольных ПК (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="c302a-1234">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="c302a-1235">Добавьте корневой веб-сайт в диспетчере служб IIS с вложенным приложением в папке внутри корневого веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="c302a-1235">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="c302a-1236">Щелкните правой кнопкой мыши папку вложенного приложения в диспетчере служб IIS и выберите **Преобразовать в приложение**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1236">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="c302a-1237">В диалоговом окне **Добавление приложения** нажмите кнопку **Выбрать**, чтобы назначить созданный **пул приложений** вложенному приложению.</span><span class="sxs-lookup"><span data-stu-id="c302a-1237">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="c302a-1238">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1238">Select **OK**.</span></span>

<span data-ttu-id="c302a-1239">Назначение отдельного пула приложений вложенному приложению является обязательным при использовании модели внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1239">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="c302a-1240">Дополнительные сведения о внутрипроцессной модели размещения и настройке модуля ASP.NET Core см. в статье <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1240">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="c302a-1241">Настройка служб IIS с помощью файла web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-1241">Configuration of IIS with web.config</span></span>

<span data-ttu-id="c302a-1242">Конфигурация IIS зависит от `<system.webServer>` раздела *web.config* для сценариев IIS, предназначенных для работы приложений ASP.NET Core с помощью модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1242">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="c302a-1243">Например, конфигурация IIS работает для динамического сжатия.</span><span class="sxs-lookup"><span data-stu-id="c302a-1243">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="c302a-1244">Если в службах IIS на уровне сервера настроено динамическое сжатие, элемент `<urlCompression>` в файле *web.config* приложения может отключить это сжатие для приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1244">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="c302a-1245">Дополнительные сведения см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="c302a-1245">For more information, see the following topics:</span></span>

* [<span data-ttu-id="c302a-1246">Справочник по настройке для \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="c302a-1246">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="c302a-1247">Сведения о настройке переменных среды для отдельных приложений, выполняющихся в изолированных пулах приложений (такая возможность поддерживается в службах IIS начиная с версии 10.0), см. в справочной документации по службам IIS, в частности в разделе *Команда AppCmd.exe* статьи [Переменные среды \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="c302a-1247">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="c302a-1248">Разделы конфигурации файла web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-1248">Configuration sections of web.config</span></span>

<span data-ttu-id="c302a-1249">Разделы конфигурации приложений ASP.NET 4.x в файле *web.config* не используются для конфигурации приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1249">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="c302a-1250">Для настройки приложений ASP.NET Core используются другие поставщики конфигураций.</span><span class="sxs-lookup"><span data-stu-id="c302a-1250">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="c302a-1251">Дополнительные сведения см. в разделе [Конфигурация](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c302a-1251">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="c302a-1252">Пулы приложений</span><span class="sxs-lookup"><span data-stu-id="c302a-1252">Application Pools</span></span>

<span data-ttu-id="c302a-1253">Модель размещения определяет изоляцию пула приложений:</span><span class="sxs-lookup"><span data-stu-id="c302a-1253">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="c302a-1254">Внутрипроцессное размещение: Приложения должны выполняться в отдельных пулах.</span><span class="sxs-lookup"><span data-stu-id="c302a-1254">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="c302a-1255">Размещение вне процесса: Рекомендуем изолировать приложения друг от друга, запуская каждое из них в собственном пуле.</span><span class="sxs-lookup"><span data-stu-id="c302a-1255">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="c302a-1256">В диалоговом окне **Добавление веб-сайта** IIS на каждое приложение по умолчанию задан один пул приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-1256">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="c302a-1257">Если указано **Имя сайта**, оно автоматически переносится в текстовое поле **Пул приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1257">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="c302a-1258">При добавлении веб-сайта создается пул приложений с именем сайта.</span><span class="sxs-lookup"><span data-stu-id="c302a-1258">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="c302a-1259">Identity пула приложений</span><span class="sxs-lookup"><span data-stu-id="c302a-1259">Application Pool Identity</span></span>

<span data-ttu-id="c302a-1260">Учетная запись удостоверения пула приложений позволяет запускать приложение от имени уникальной учетной записи, не создавая домены или локальные учетные записи и не управляя ими.</span><span class="sxs-lookup"><span data-stu-id="c302a-1260">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="c302a-1261">В службах IIS начиная с версии 8.0 рабочий процесс администратора IIS (WAS) создает виртуальную учетную запись с именем нового пула приложений и по умолчанию выполняет рабочие процессы пула приложений с помощью этой учетной записи.</span><span class="sxs-lookup"><span data-stu-id="c302a-1261">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="c302a-1262">В консоли управления IIS в окне **Дополнительные параметры** пула приложений для **Identity** необходимо выбрать **ApplicationPoolIdentity**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1262">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Диалоговое окно дополнительных параметров для пула приложений](index/_static/apppool-identity.png)

<span data-ttu-id="c302a-1264">Процесс управления IIS создает в системе безопасности Windows безопасный идентификатор с именем пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-1264">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="c302a-1265">С помощью этого идентификатора можно защищать ресурсы,</span><span class="sxs-lookup"><span data-stu-id="c302a-1265">Resources can be secured using this identity.</span></span> <span data-ttu-id="c302a-1266">но он не является реальной учетной записью и не отображается в консоли управления пользователями Windows.</span><span class="sxs-lookup"><span data-stu-id="c302a-1266">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="c302a-1267">Если рабочему процессу IIS требуется предоставить доступ к приложению с повышенными правами, измените список управления доступом (ACL) для каталога, содержащего приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-1267">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="c302a-1268">Откройте проводник и перейдите к каталогу.</span><span class="sxs-lookup"><span data-stu-id="c302a-1268">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="c302a-1269">Щелкните каталог правой кнопкой мыши и выберите пункт **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1269">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="c302a-1270">На вкладке **Безопасность** нажмите кнопку **Изменить**, а затем — кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1270">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="c302a-1271">Нажмите кнопку **Размещение** и выберите систему.</span><span class="sxs-lookup"><span data-stu-id="c302a-1271">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="c302a-1272">В поле **Введите имена выбираемых объектов** введите **IIS AppPool\\<имя_пула_приложений>** .</span><span class="sxs-lookup"><span data-stu-id="c302a-1272">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="c302a-1273">Нажмите кнопку **Проверить имена**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1273">Select the **Check Names** button.</span></span> <span data-ttu-id="c302a-1274">В случае с *DefaultAppPool* проверьте имена с помощью **IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1274">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="c302a-1275">После нажатия кнопки **Проверить имена** в области имен объектов отобразится значение **DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1275">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="c302a-1276">Вручную ввести имя пула приложений в области имен объектов нельзя.</span><span class="sxs-lookup"><span data-stu-id="c302a-1276">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="c302a-1277">При поиске имени объекта используйте формат **IIS AppPool\\<имя_пула_приложений>** .</span><span class="sxs-lookup"><span data-stu-id="c302a-1277">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Диалоговое окно "Выбор пользователей или групп" для папки приложения. До нажатия кнопки "Проверить имена" в области имен объектов к строке IIS AppPool\" добавилось имя пула приложений, DefaultAppPool.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="c302a-1279">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1279">Select **OK**.</span></span>

   ![Диалоговое окно "Выбор пользователей или групп" для папки приложения. После нажатия кнопки "Проверить имена" в области имен объектов отображается имя пула приложений, DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="c302a-1281">Разрешения на чтение и выполнение предоставляются по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c302a-1281">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="c302a-1282">При необходимости предоставьте дополнительные разрешения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1282">Provide additional permissions as needed.</span></span>

<span data-ttu-id="c302a-1283">Кроме того, доступ можно предоставить через командную строку, используя средство **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1283">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="c302a-1284">В случае с *DefaultAppPool* выполняется такая команда:</span><span class="sxs-lookup"><span data-stu-id="c302a-1284">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="c302a-1285">Дополнительные сведения об ICACLS см. [здесь](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="c302a-1285">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="c302a-1286">Поддержка HTTP/2</span><span class="sxs-lookup"><span data-stu-id="c302a-1286">HTTP/2 support</span></span>

<span data-ttu-id="c302a-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html) поддерживается в ASP.NET Core для следующих сценариев развертывания IIS:</span><span class="sxs-lookup"><span data-stu-id="c302a-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="c302a-1288">Внутрипроцессно</span><span class="sxs-lookup"><span data-stu-id="c302a-1288">In-process</span></span>
  * <span data-ttu-id="c302a-1289">Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="c302a-1289">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c302a-1290">Подключение TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="c302a-1290">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="c302a-1291">Внепроцессно</span><span class="sxs-lookup"><span data-stu-id="c302a-1291">Out-of-process</span></span>
  * <span data-ttu-id="c302a-1292">Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="c302a-1292">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c302a-1293">Подключения к пограничным серверам, открытых для общего доступа, выполняются по протоколу HTTP/2, а подключения к [серверу Kestrel](xref:fundamentals/servers/kestrel) через обратный прокси-сервер — по HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c302a-1293">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="c302a-1294">Подключение TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="c302a-1294">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c302a-1295">При внутрипроцессном развертывании и установленном подключении HTTP/2 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1295">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="c302a-1296">При внепроцессном развертывании и установленном подключении HTTP/2 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1296">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="c302a-1297">Дополнительные сведения о внутрипроцессной и внепроцессной моделях размещения см. в статье <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1297">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="c302a-1298">Протокол HTTP/2 включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c302a-1298">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="c302a-1299">Если не удается установить подключение HTTP/2, применяется резервный вариант HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c302a-1299">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="c302a-1300">Дополнительные сведения о настройке HTTP/2 для развертываний IIS см. в статье [об HTTP/2 в IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="c302a-1300">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="c302a-1301">Предварительные запросы CORS</span><span class="sxs-lookup"><span data-stu-id="c302a-1301">CORS preflight requests</span></span>

<span data-ttu-id="c302a-1302">*Этот раздел относится только к приложениям ASP.NET Core, предназначенным для .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="c302a-1302">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="c302a-1303">Для приложения ASP.NET Core, предназначенного для .NET Framework, параметры OPTIONS не передаются в приложение по умолчанию в службах IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1303">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="c302a-1304">Чтобы узнать, как настроить обработчики приложения IIS в файле *web.config* для передачи запросов OPTIONS, см. раздел [Enable cross-origin requests in ASP.NET Web API 2. How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works) (Включение запросов CORS в ASP.NET Web API 2. Принцип работы CORS).</span><span class="sxs-lookup"><span data-stu-id="c302a-1304">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="c302a-1305">Модуль инициализации приложений и время ожидания в режиме простоя</span><span class="sxs-lookup"><span data-stu-id="c302a-1305">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="c302a-1306">Размещение в IIS с помощью модуля ASP.NET Core версии 2:</span><span class="sxs-lookup"><span data-stu-id="c302a-1306">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="c302a-1307">[Модуль инициализации приложений](#application-initialization-module). Приложение, размещенное [в процессе](#in-process-hosting-model) или [вне процесса](#out-of-process-hosting-model), можно настроить на автоматический запуск при перезапуске рабочего процесса или сервера.</span><span class="sxs-lookup"><span data-stu-id="c302a-1307">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="c302a-1308">[Время ожидания в режиме простоя](#idle-timeout). Приложение, размещенное [в процессе](#in-process-hosting-model), можно настроить на игнорирование времени ожидания в периоды неактивности.</span><span class="sxs-lookup"><span data-stu-id="c302a-1308">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="c302a-1309">Модуль инициализации приложений</span><span class="sxs-lookup"><span data-stu-id="c302a-1309">Application Initialization Module</span></span>

<span data-ttu-id="c302a-1310">*Применяется к приложениям, размещенным в процессе и вне процесса.*</span><span class="sxs-lookup"><span data-stu-id="c302a-1310">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="c302a-1311">Функция [инициализации приложений](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) в IIS отправляет в приложение HTTP-запрос при запуске или перезапуске пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-1311">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="c302a-1312">Этот запрос инициирует запуск приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1312">The request triggers the app to start.</span></span> <span data-ttu-id="c302a-1313">По умолчанию IIS отправляет запрос к корневому URL-адресу приложения (`/`) для его инициализации (подробные сведения о конфигурации см. в [дополнительных ресурсах](#application-initialization-module-and-idle-timeout-additional-resources)).</span><span class="sxs-lookup"><span data-stu-id="c302a-1313">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="c302a-1314">Убедитесь, что включена роль инициализации приложения IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1314">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="c302a-1315">На настольных компьютерах с Windows 7 или более поздней версии при локальном использовании IIS:</span><span class="sxs-lookup"><span data-stu-id="c302a-1315">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="c302a-1316">Последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).</span><span class="sxs-lookup"><span data-stu-id="c302a-1316">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="c302a-1317">Откройте **Службы IIS** > **Службы Интернета** > **Компоненты разработки приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1317">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="c302a-1318">Установите флажок **Инициализация приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1318">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="c302a-1319">В Windows Server 2008 R2 и более поздней версии:</span><span class="sxs-lookup"><span data-stu-id="c302a-1319">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="c302a-1320">Откройте **Мастер добавления ролей и компонентов**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1320">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="c302a-1321">На панели **Выбор служб ролей** разверните узел **Разработка приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1321">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="c302a-1322">Установите флажок **Инициализация приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1322">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="c302a-1323">Чтобы включить модуль инициализации приложений для сайта, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="c302a-1323">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="c302a-1324">При использовании диспетчера IIS:</span><span class="sxs-lookup"><span data-stu-id="c302a-1324">Using IIS Manager:</span></span>

  1. <span data-ttu-id="c302a-1325">Выберите **Пулы приложений** на панели **Подключения**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1325">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="c302a-1326">Щелкните пул приложений в списке правой кнопкой мыши и выберите **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="c302a-1327">Для **режима запуска** по умолчанию задано значение **OnDemand**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1327">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="c302a-1328">Выберите для параметра **Режим запуска** значение **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1328">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="c302a-1329">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1329">Select **OK**.</span></span>
  1. <span data-ttu-id="c302a-1330">Откройте узел **Сайты** на панели **Подключения**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1330">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="c302a-1331">Щелкните приложение правой кнопкой мыши и выберите **Управление веб-сайтом** > **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1331">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="c302a-1332">По умолчанию для параметра **Предварительная загрузка включена** установлено значение **False**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1332">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="c302a-1333">Для параметра **Предварительная загрузка включена** выберите значение **True**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1333">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="c302a-1334">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1334">Select **OK**.</span></span>

* <span data-ttu-id="c302a-1335">Откройте файл *web.config* и добавьте элемент `<applicationInitialization>` с параметром `doAppInitAfterRestart`, для которого установлено значение `true`, к элементам `<system.webServer>` в файле *web.config* приложения:</span><span class="sxs-lookup"><span data-stu-id="c302a-1335">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="c302a-1336">Время ожидания в режиме простоя</span><span class="sxs-lookup"><span data-stu-id="c302a-1336">Idle Timeout</span></span>

<span data-ttu-id="c302a-1337">*Применяется только к приложениям, размещенным в процессе.*</span><span class="sxs-lookup"><span data-stu-id="c302a-1337">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="c302a-1338">Чтобы предотвратить переход приложения из состояния простоя, задайте для пула приложений время ожидания в режиме простоя с помощью диспетчера IIS:</span><span class="sxs-lookup"><span data-stu-id="c302a-1338">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="c302a-1339">Выберите **Пулы приложений** на панели **Подключения**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1339">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="c302a-1340">Щелкните пул приложений в списке правой кнопкой мыши и выберите **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1340">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="c302a-1341">По умолчанию для параметра **Тайм-аут простоя (в минутах)** установлено значение **20** минут.</span><span class="sxs-lookup"><span data-stu-id="c302a-1341">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="c302a-1342">Задайте для параметра **Тайм-аут простоя (в минутах)** значение **0**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1342">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="c302a-1343">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1343">Select **OK**.</span></span>
1. <span data-ttu-id="c302a-1344">Перезапустите рабочий процесс.</span><span class="sxs-lookup"><span data-stu-id="c302a-1344">Recycle the worker process.</span></span>

<span data-ttu-id="c302a-1345">Чтобы не истекло время ожидания в приложениях, размещенных [вне процесса](#out-of-process-hosting-model), воспользуйтесь одним из таких методов:</span><span class="sxs-lookup"><span data-stu-id="c302a-1345">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="c302a-1346">Проверьте связь с приложением из внешней службы, чтобы оно продолжило работу.</span><span class="sxs-lookup"><span data-stu-id="c302a-1346">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="c302a-1347">Если приложение размещает только фоновые службы, избегайте размещения в службах IIS и воспользуйтесь [службой Windows для размещения приложения ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="c302a-1347">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="c302a-1348">Дополнительные ресурсы по модулю инициализации приложений и времени ожидания в режиме простоя</span><span class="sxs-lookup"><span data-stu-id="c302a-1348">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="c302a-1349">Инициализация приложений в IIS 8.0</span><span class="sxs-lookup"><span data-stu-id="c302a-1349">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="c302a-1350">[Инициализация приложений \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="c302a-1350">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="c302a-1351">[Параметры модели процессов для пула приложений \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="c302a-1351">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="c302a-1352">Ресурсы развертывания для администраторов IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1352">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="c302a-1353">Документация по службам IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1353">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="c302a-1354">Начало работы с диспетчером IIS в IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1354">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="c302a-1355">Развертывание приложений .NET Core</span><span class="sxs-lookup"><span data-stu-id="c302a-1355">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="c302a-1356">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c302a-1356">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="c302a-1357">Официальный веб-сайт Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1357">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="c302a-1358">Библиотека технического содержимого по Windows Server</span><span class="sxs-lookup"><span data-stu-id="c302a-1358">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="c302a-1359">HTTP/2 в IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1359">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c302a-1360">Руководство по публикации приложения ASP.NET Core на сервере IIS см. по этой ссылке: <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1360">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="c302a-1361">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="c302a-1361">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="c302a-1362">Поддерживаемые операционные системы</span><span class="sxs-lookup"><span data-stu-id="c302a-1362">Supported operating systems</span></span>

<span data-ttu-id="c302a-1363">Поддерживаются следующие операционные системы:</span><span class="sxs-lookup"><span data-stu-id="c302a-1363">The following operating systems are supported:</span></span>

* <span data-ttu-id="c302a-1364">Windows 7 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="c302a-1364">Windows 7 or later</span></span>
* <span data-ttu-id="c302a-1365">Windows Server 2008 R2 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="c302a-1365">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c302a-1366">[Сервер HTTP.sys](xref:fundamentals/servers/httpsys) (ранее назывался WebListener) не работает в конфигурации обратного прокси-сервера со службами IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1366">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="c302a-1367">Используйте [сервер Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="c302a-1367">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="c302a-1368">Сведения о размещении в Azure см. в статье <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1368">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="c302a-1369">Рекомендации по устранению неполадок см. в статье <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1369">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="c302a-1370">Поддерживаемые платформы</span><span class="sxs-lookup"><span data-stu-id="c302a-1370">Supported platforms</span></span>

<span data-ttu-id="c302a-1371">Поддерживаются приложения, опубликованные для развертывания в 32-разрядных (x86) или 64-разрядных (x64) системах.</span><span class="sxs-lookup"><span data-stu-id="c302a-1371">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="c302a-1372">Развертывайте 32-разрядную версию (x86) приложения с использованием 32-разрядного пакета SDK для .NET Core, кроме следующих случаев:</span><span class="sxs-lookup"><span data-stu-id="c302a-1372">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="c302a-1373">Приложению требуется более широкое адресное пространство виртуальной памяти, доступное в 64-разрядной версии приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1373">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="c302a-1374">Приложению требуется стек IIS большего размера.</span><span class="sxs-lookup"><span data-stu-id="c302a-1374">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="c302a-1375">В коде присутствуют зависимости 64-разрядной версии.</span><span class="sxs-lookup"><span data-stu-id="c302a-1375">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="c302a-1376">Используйте 64-разрядный (x64) пакет SDK для .NET Core для публикации 64-разрядной версии приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1376">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="c302a-1377">Для этого в системе узла должна присутствовать 64-разрядная версия среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1377">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="c302a-1378">ASP.NET Core поставляется с [сервером Kestrel](xref:fundamentals/servers/kestrel), который является кроссплатформенным HTTP-сервером по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c302a-1378">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="c302a-1379">При использовании [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) или [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) приложение запускается отдельно от рабочего процесса IIS (*внепроцессно*) с [сервером Kestrel](xref:fundamentals/servers/index#kestrel).</span><span class="sxs-lookup"><span data-stu-id="c302a-1379">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="c302a-1380">Так как приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, этот модуль обрабатывает управление процессами.</span><span class="sxs-lookup"><span data-stu-id="c302a-1380">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="c302a-1381">Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="c302a-1381">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="c302a-1382">Это, по сути, совпадает с поведением приложений, выполняемых внутрипроцессно и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="c302a-1382">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c302a-1383">На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением, размещенным вне процесса.</span><span class="sxs-lookup"><span data-stu-id="c302a-1383">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Модуль ASP.NET Core](index/_static/ancm-outofprocess.png)

<span data-ttu-id="c302a-1385">Запросы поступают из Интернета в драйвер HTTP.sys в режиме ядра.</span><span class="sxs-lookup"><span data-stu-id="c302a-1385">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="c302a-1386">Драйвер направляет запросы к службам IIS на настроенный порт веб-сайта — обычно 80 (HTTP) или 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c302a-1386">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="c302a-1387">Модуль перенаправляет запросы Kestrel на случайный порт для приложения, отличающийся от порта 80 или 443.</span><span class="sxs-lookup"><span data-stu-id="c302a-1387">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="c302a-1388">Модуль задает порт с помощью переменной среды во время запуска, а [ПО промежуточного слоя для интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) настраивает сервер для прослушивания `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1388">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="c302a-1389">Выполняются дополнительные проверки, и запросы не из модуля отклоняются.</span><span class="sxs-lookup"><span data-stu-id="c302a-1389">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="c302a-1390">Модуль не поддерживает переадресацию по HTTPS, поэтому запросы переадресовываются по протоколу HTTP, даже если были получены IIS по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1390">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="c302a-1391">После того как Kestrel забирает запрос из модуля, запрос передается в конвейер ПО промежуточного слоя ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1391">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="c302a-1392">Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1392">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="c302a-1393">ПО промежуточного слоя, добавленное интеграцией IIS, обновляет схему, удаленный IP-адрес и базовый путь для переадресации запроса в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c302a-1393">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="c302a-1394">Отклик приложения передается обратно в службу IIS, которая отправляет его обратно в HTTP-клиент, инициировавший запрос.</span><span class="sxs-lookup"><span data-stu-id="c302a-1394">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="c302a-1395">`CreateDefaultBuilder` настраивает сервер [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера и активирует интеграцию IIS, задавая базовый путь и порт для [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c302a-1395">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="c302a-1396">Модуль ASP.NET Core создает динамический порт для назначения серверному процессу.</span><span class="sxs-lookup"><span data-stu-id="c302a-1396">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="c302a-1397">`CreateDefaultBuilder` вызывает метод <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1397">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="c302a-1398">`UseIISIntegration` настраивает Kestrel для прослушивания динамического порта по IP-адресу localhost (`127.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="c302a-1398">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="c302a-1399">Если динамический порт — 1234, Kestrel прослушивает `127.0.0.1:1234`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1399">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="c302a-1400">Эта конфигурация заменяет другие конфигурации URL-адресов, предоставляемые:</span><span class="sxs-lookup"><span data-stu-id="c302a-1400">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* <span data-ttu-id="c302a-1401">[API прослушивания Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration);</span><span class="sxs-lookup"><span data-stu-id="c302a-1401">[Kestrel's Listen API](xref:fundamentals/servers/kestrel#endpoint-configuration)</span></span>
* <span data-ttu-id="c302a-1402">[Конфигурацией](xref:fundamentals/configuration/index) (или [параметром командной строки--urls](xref:fundamentals/host/web-host#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="c302a-1402">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="c302a-1403">Вызовы `UseUrls` или API `Listen` Kestrel при работе с этим модулем не требуются.</span><span class="sxs-lookup"><span data-stu-id="c302a-1403">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="c302a-1404">При вызове `UseUrls` или `Listen` Kestrel ожидает передачи данных на порт, указанный только при выполнении приложения без IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1404">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="c302a-1405">Инструкции по настройке модуля ASP.NET Core см. в статье <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1405">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="c302a-1406">Дополнительные сведения о размещении см. в разделе [Размещение в ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="c302a-1406">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="c302a-1407">Настройка приложения</span><span class="sxs-lookup"><span data-stu-id="c302a-1407">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="c302a-1408">Включение компонентов IISIntegration</span><span class="sxs-lookup"><span data-stu-id="c302a-1408">Enable the IISIntegration components</span></span>

<span data-ttu-id="c302a-1409">При создании узла в `CreateWebHostBuilder` (*Program.cs*) вызовите метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, чтобы включить интеграцию IIS:</span><span class="sxs-lookup"><span data-stu-id="c302a-1409">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="c302a-1410">Дополнительные сведения о методе `CreateDefaultBuilder` см. в разделе <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1410">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="c302a-1411">Параметры служб IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1411">IIS options</span></span>

| <span data-ttu-id="c302a-1412">Параметр</span><span class="sxs-lookup"><span data-stu-id="c302a-1412">Option</span></span>                         | <span data-ttu-id="c302a-1413">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c302a-1413">Default</span></span> | <span data-ttu-id="c302a-1414">Параметр</span><span class="sxs-lookup"><span data-stu-id="c302a-1414">Setting</span></span> |
| ---
<span data-ttu-id="c302a-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1416">'Blazor'</span></span>
- <span data-ttu-id="c302a-1417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1417">'Identity'</span></span>
- <span data-ttu-id="c302a-1418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1418">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1419">'Razor'</span></span>
- <span data-ttu-id="c302a-1420">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1422">'Blazor'</span></span>
- <span data-ttu-id="c302a-1423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1423">'Identity'</span></span>
- <span data-ttu-id="c302a-1424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1424">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1425">'Razor'</span></span>
- <span data-ttu-id="c302a-1426">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1428">'Blazor'</span></span>
- <span data-ttu-id="c302a-1429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1429">'Identity'</span></span>
- <span data-ttu-id="c302a-1430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1430">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1431">'Razor'</span></span>
- <span data-ttu-id="c302a-1432">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1434">'Blazor'</span></span>
- <span data-ttu-id="c302a-1435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1435">'Identity'</span></span>
- <span data-ttu-id="c302a-1436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1436">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1437">'Razor'</span></span>
- <span data-ttu-id="c302a-1438">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1440">'Blazor'</span></span>
- <span data-ttu-id="c302a-1441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1441">'Identity'</span></span>
- <span data-ttu-id="c302a-1442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1442">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1443">'Razor'</span></span>
- <span data-ttu-id="c302a-1444">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1446">'Blazor'</span></span>
- <span data-ttu-id="c302a-1447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1447">'Identity'</span></span>
- <span data-ttu-id="c302a-1448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1448">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1449">'Razor'</span></span>
- <span data-ttu-id="c302a-1450">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1452">'Blazor'</span></span>
- <span data-ttu-id="c302a-1453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1453">'Identity'</span></span>
- <span data-ttu-id="c302a-1454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1454">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1455">'Razor'</span></span>
- <span data-ttu-id="c302a-1456">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1458">'Blazor'</span></span>
- <span data-ttu-id="c302a-1459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1459">'Identity'</span></span>
- <span data-ttu-id="c302a-1460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1460">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1461">'Razor'</span></span>
- <span data-ttu-id="c302a-1462">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1464">'Blazor'</span></span>
- <span data-ttu-id="c302a-1465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1465">'Identity'</span></span>
- <span data-ttu-id="c302a-1466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1466">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1467">'Razor'</span></span>
- <span data-ttu-id="c302a-1468">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1470">'Blazor'</span></span>
- <span data-ttu-id="c302a-1471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1471">'Identity'</span></span>
- <span data-ttu-id="c302a-1472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1472">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1473">'Razor'</span></span>
- <span data-ttu-id="c302a-1474">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1476">'Blazor'</span></span>
- <span data-ttu-id="c302a-1477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1477">'Identity'</span></span>
- <span data-ttu-id="c302a-1478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1478">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1479">'Razor'</span></span>
- <span data-ttu-id="c302a-1480">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1480">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1482">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1482">'Blazor'</span></span>
- <span data-ttu-id="c302a-1483">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1483">'Identity'</span></span>
- <span data-ttu-id="c302a-1484">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1484">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1485">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1485">'Razor'</span></span>
- <span data-ttu-id="c302a-1486">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1486">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1488">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1488">'Blazor'</span></span>
- <span data-ttu-id="c302a-1489">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1489">'Identity'</span></span>
- <span data-ttu-id="c302a-1490">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1490">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1491">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1491">'Razor'</span></span>
- <span data-ttu-id="c302a-1492">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1492">'SignalR' uid:</span></span> 

<span data-ttu-id="c302a-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1494">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1494">'Blazor'</span></span>
- <span data-ttu-id="c302a-1495">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1495">'Identity'</span></span>
- <span data-ttu-id="c302a-1496">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1496">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1497">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1497">'Razor'</span></span>
- <span data-ttu-id="c302a-1498">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1498">'SignalR' uid:</span></span> 

<span data-ttu-id="c302a-1499">---- | | `AutomaticAuthentication`      | `true`  | Если значение — `true`, сервер IIS задает свойство `HttpContext.User`, использующее [проверку подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-1499">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c302a-1500">Если значение — `false`, сервер только предоставляет идентификатор для `HttpContext.User` и отвечает на явные запросы защиты от `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1500">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="c302a-1501">Для работы `AutomaticAuthentication` необходимо включить в службах IIS проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="c302a-1501">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="c302a-1502">Дополнительные сведения: [Проверка подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-1502">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c302a-1503">| | `AuthenticationDisplayName`    | `null`  | Задает отображаемое имя для пользователей на страницах входа.</span><span class="sxs-lookup"><span data-stu-id="c302a-1503">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="c302a-1504">Чтобы настроить параметры IIS, включите конфигурацию служб для <xref:Microsoft.AspNetCore.Builder.IISOptions> в <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1504">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="c302a-1505">В следующем примере приложению запрещается заполнение `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="c302a-1505">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="c302a-1506">Параметр</span><span class="sxs-lookup"><span data-stu-id="c302a-1506">Option</span></span>                         | <span data-ttu-id="c302a-1507">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c302a-1507">Default</span></span> | <span data-ttu-id="c302a-1508">Параметр</span><span class="sxs-lookup"><span data-stu-id="c302a-1508">Setting</span></span> |
| ---
<span data-ttu-id="c302a-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1510">'Blazor'</span></span>
- <span data-ttu-id="c302a-1511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1511">'Identity'</span></span>
- <span data-ttu-id="c302a-1512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1512">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1513">'Razor'</span></span>
- <span data-ttu-id="c302a-1514">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1516">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1516">'Blazor'</span></span>
- <span data-ttu-id="c302a-1517">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1517">'Identity'</span></span>
- <span data-ttu-id="c302a-1518">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1518">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1519">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1519">'Razor'</span></span>
- <span data-ttu-id="c302a-1520">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1520">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1522">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1522">'Blazor'</span></span>
- <span data-ttu-id="c302a-1523">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1523">'Identity'</span></span>
- <span data-ttu-id="c302a-1524">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1524">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1525">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1525">'Razor'</span></span>
- <span data-ttu-id="c302a-1526">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1526">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1528">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1528">'Blazor'</span></span>
- <span data-ttu-id="c302a-1529">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1529">'Identity'</span></span>
- <span data-ttu-id="c302a-1530">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1530">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1531">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1531">'Razor'</span></span>
- <span data-ttu-id="c302a-1532">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1532">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1534">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1534">'Blazor'</span></span>
- <span data-ttu-id="c302a-1535">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1535">'Identity'</span></span>
- <span data-ttu-id="c302a-1536">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1536">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1537">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1537">'Razor'</span></span>
- <span data-ttu-id="c302a-1538">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1538">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1540">'Blazor'</span></span>
- <span data-ttu-id="c302a-1541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1541">'Identity'</span></span>
- <span data-ttu-id="c302a-1542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1542">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1543">'Razor'</span></span>
- <span data-ttu-id="c302a-1544">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1546">'Blazor'</span></span>
- <span data-ttu-id="c302a-1547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1547">'Identity'</span></span>
- <span data-ttu-id="c302a-1548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1548">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1549">'Razor'</span></span>
- <span data-ttu-id="c302a-1550">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1552">'Blazor'</span></span>
- <span data-ttu-id="c302a-1553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1553">'Identity'</span></span>
- <span data-ttu-id="c302a-1554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1554">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1555">'Razor'</span></span>
- <span data-ttu-id="c302a-1556">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1558">'Blazor'</span></span>
- <span data-ttu-id="c302a-1559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1559">'Identity'</span></span>
- <span data-ttu-id="c302a-1560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1560">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1561">'Razor'</span></span>
- <span data-ttu-id="c302a-1562">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1564">'Blazor'</span></span>
- <span data-ttu-id="c302a-1565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1565">'Identity'</span></span>
- <span data-ttu-id="c302a-1566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1566">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1567">'Razor'</span></span>
- <span data-ttu-id="c302a-1568">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1570">'Blazor'</span></span>
- <span data-ttu-id="c302a-1571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1571">'Identity'</span></span>
- <span data-ttu-id="c302a-1572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1572">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1573">'Razor'</span></span>
- <span data-ttu-id="c302a-1574">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1576">'Blazor'</span></span>
- <span data-ttu-id="c302a-1577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1577">'Identity'</span></span>
- <span data-ttu-id="c302a-1578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1578">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1579">'Razor'</span></span>
- <span data-ttu-id="c302a-1580">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c302a-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1582">'Blazor'</span></span>
- <span data-ttu-id="c302a-1583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1583">'Identity'</span></span>
- <span data-ttu-id="c302a-1584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1584">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1585">'Razor'</span></span>
- <span data-ttu-id="c302a-1586">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1586">'SignalR' uid:</span></span> 

<span data-ttu-id="c302a-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c302a-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c302a-1588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1588">'Blazor'</span></span>
- <span data-ttu-id="c302a-1589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c302a-1589">'Identity'</span></span>
- <span data-ttu-id="c302a-1590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c302a-1590">'Let's Encrypt'</span></span>
- <span data-ttu-id="c302a-1591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c302a-1591">'Razor'</span></span>
- <span data-ttu-id="c302a-1592">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c302a-1592">'SignalR' uid:</span></span> 

<span data-ttu-id="c302a-1593">---- | | `AutomaticAuthentication`      | `true`  | Если значение — `true`, [ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components) задает свойство `HttpContext.User`, которое прошло [проверку подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-1593">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c302a-1594">Если значение — `false`, ПО промежуточного слоя только предоставляет идентификатор для `HttpContext.User` и отвечает на явные запросы защиты от `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1594">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="c302a-1595">Для работы `AutomaticAuthentication` необходимо включить в службах IIS проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="c302a-1595">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="c302a-1596">Дополнительные сведения см. в статье о [проверке подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-1596">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="c302a-1597">| | `AuthenticationDisplayName`    | `null`  | Задает отображаемое имя для пользователей на страницах входа.</span><span class="sxs-lookup"><span data-stu-id="c302a-1597">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="c302a-1598">| | `ForwardClientCertificate`     | `true`  | Если значение — `true` и если присутствует заголовок запроса `MS-ASPNETCORE-CLIENTCERT`, происходит заполнение `HttpContext.Connection.ClientCertificate`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1598">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c302a-1599">Сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="c302a-1599">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c302a-1600">[ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components), которое настраивает ПО промежуточного слоя переадресации заголовков, и модуль ASP.NET Core настраиваются на пересылку схемы (HTTP/HTTPS) и удаленного IP-адреса расположения, где был сформирован запрос.</span><span class="sxs-lookup"><span data-stu-id="c302a-1600">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="c302a-1601">Для приложений, размещенных за дополнительными прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="c302a-1601">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="c302a-1602">Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="c302a-1602">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="c302a-1603">Файл web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-1603">web.config file</span></span>

<span data-ttu-id="c302a-1604">В файле *web.config* содержится конфигурация [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c302a-1604">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="c302a-1605">Создание, преобразование и публикация файла *web.config* обрабатываются целевым объектом MSBuild (`_TransformWebConfig`) при публикации проекта.</span><span class="sxs-lookup"><span data-stu-id="c302a-1605">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="c302a-1606">Этот целевой объект присутствует в целевых веб-пакетах SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="c302a-1606">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="c302a-1607">Пакет SDK задается в начале файла проекта:</span><span class="sxs-lookup"><span data-stu-id="c302a-1607">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="c302a-1608">Если в проекте нет файла *web.config*, он создается с соответствующими аргументами *processPath* и *arguments* для настройки модуля ASP.NET Core и переносится в [опубликованные выходные данные](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="c302a-1608">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="c302a-1609">Если в проекте есть файл *web.config*, он преобразуется с соответствующими аргументами *processPath* и *arguments* для настройки модуля ASP.NET Core и переносится в опубликованные выходные данные.</span><span class="sxs-lookup"><span data-stu-id="c302a-1609">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="c302a-1610">Преобразование не изменяет параметры конфигурации служб IIS, включенные в файл.</span><span class="sxs-lookup"><span data-stu-id="c302a-1610">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="c302a-1611">Файл *web.config* может содержать дополнительные параметры конфигурации IIS, управляющие активными модулями IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1611">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="c302a-1612">Сведения о модулях IIS, которые могут обрабатывать запросы к приложениям ASP.NET Core, см. в статье [Модули IIS](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="c302a-1612">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="c302a-1613">Чтобы пакет SDK не преобразовывал файл *web.config*, добавьте в файл проекта свойство **\<IsTransformWebConfigDisabled>** :</span><span class="sxs-lookup"><span data-stu-id="c302a-1613">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="c302a-1614">Если пакет SDK не преобразует файл, аргументы *processPath* и *arguments* нужно задать вручную.</span><span class="sxs-lookup"><span data-stu-id="c302a-1614">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="c302a-1615">Для получения дополнительной информации см. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1615">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="c302a-1616">Расположение файла web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-1616">web.config file location</span></span>

<span data-ttu-id="c302a-1617">Для корректной настройки [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module) необходимо наличие файла *web.config* в [корневой папке содержимого](xref:fundamentals/index#content-root) развертываемого приложения (как правило, это основной путь приложения).</span><span class="sxs-lookup"><span data-stu-id="c302a-1617">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="c302a-1618">Это расположение соответствует физическому пути веб-сайта, указанному в службах IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1618">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="c302a-1619">Файл *web.config* требуется в корне приложения, чтобы разрешить публикацию нескольких приложений с помощью веб-развертывания.</span><span class="sxs-lookup"><span data-stu-id="c302a-1619">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="c302a-1620">По физическому пути приложения находятся файлы с конфиденциальной информацией: *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (комментарии к XML-документации) и *\<assembly>.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="c302a-1620">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="c302a-1621">Когда файл *web.config* присутствует и сайт запускается нормально, службы IIS не обрабатывают запросы к этим файлам.</span><span class="sxs-lookup"><span data-stu-id="c302a-1621">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="c302a-1622">Если файл *web.config* отсутствует, неправильно именован или не может настроить нормальный запуск сайта, службы IIS могут свободно передавать содержимое этих конфиденциальных файлов.</span><span class="sxs-lookup"><span data-stu-id="c302a-1622">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="c302a-1623">**Файл *web.config* должен постоянно находиться в развертывании, у этого файла должно быть правильное имя, и файл должен быть в состоянии настроить нормальный запуск сайта. Никогда не удаляйте файл *web.config* из развертывания в рабочей среде.**</span><span class="sxs-lookup"><span data-stu-id="c302a-1623">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="c302a-1624">Преобразование web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-1624">Transform web.config</span></span>

<span data-ttu-id="c302a-1625">Если вам нужно преобразовать *web.config* при публикации (например, задать переменные среды на основе конфигурации, профиля или среды), см. раздел <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1625">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="c302a-1626">Конфигурация IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1626">IIS configuration</span></span>

<span data-ttu-id="c302a-1627">**Операционные системы семейства Windows Server**</span><span class="sxs-lookup"><span data-stu-id="c302a-1627">**Windows Server operating systems**</span></span>

<span data-ttu-id="c302a-1628">Включите роль сервера **Веб-сервер (IIS)** и настройте службы роли.</span><span class="sxs-lookup"><span data-stu-id="c302a-1628">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="c302a-1629">В меню **Управление** запустите мастер **Добавить роли и компоненты** или в окне **Диспетчер серверов** щелкните соответствующую ссылку.</span><span class="sxs-lookup"><span data-stu-id="c302a-1629">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="c302a-1630">На этапе **Роли сервера** установите флажок **Веб-сервер (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="c302a-1630">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Роль "Веб-сервер (IIS)" выбрана на странице "Выбор ролей сервера".](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="c302a-1632">После этапа выбора **компонентов** запускается этап выбора **служб роли** для веб-сервера (IIS).</span><span class="sxs-lookup"><span data-stu-id="c302a-1632">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="c302a-1633">Выберите нужные службы роли IIS или оставьте службы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c302a-1633">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Службы роли по умолчанию, выбранные на странице "Выбор служб ролей".](index/_static/role-services-ws2016.png)

   <span data-ttu-id="c302a-1635">**Проверка подлинности Windows (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c302a-1635">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c302a-1636">Чтобы включить проверку подлинности Windows, разверните такие узлы: **Веб-сервер** > **Безопасность**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1636">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="c302a-1637">Выберите компонент **Проверка подлинности Windows**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1637">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c302a-1638">Дополнительные сведения см. в статьях [Проверка подлинности Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) и [Настройка проверки подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-1638">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c302a-1639">**Протокол WebSocket (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c302a-1639">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c302a-1640">Протокол WebSocket поддерживается в ASP.NET Core начиная с версии 1.1.</span><span class="sxs-lookup"><span data-stu-id="c302a-1640">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c302a-1641">Чтобы включить протокол WebSocket, разверните такие узлы: **Веб-сервер** > **Разработка приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1641">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="c302a-1642">Выберите компонент **Протокол WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1642">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c302a-1643">Дополнительные сведения см. в разделе [Протокол WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="c302a-1643">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c302a-1644">Пройдите этап **Подтверждение**, чтобы установить роль и службы веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="c302a-1644">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="c302a-1645">После установки роли **Веб-сервер (IIS)** перезагружать сервер или службы IIS не требуется.</span><span class="sxs-lookup"><span data-stu-id="c302a-1645">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="c302a-1646">**Операционные системы Windows для настольных компьютеров**</span><span class="sxs-lookup"><span data-stu-id="c302a-1646">**Windows desktop operating systems**</span></span>

<span data-ttu-id="c302a-1647">Включите компоненты **Консоль управления IIS** и **Службы Интернета**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1647">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c302a-1648">Последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).</span><span class="sxs-lookup"><span data-stu-id="c302a-1648">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="c302a-1649">Разверните узел **Службы IIS**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1649">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="c302a-1650">Разверните узел **Средства управления веб-сайтом**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1650">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="c302a-1651">Установите флажок **Консоль управления IIS**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1651">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="c302a-1652">Установите флажок **Службы Интернета**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1652">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c302a-1653">В группе **Службы Интернета** оставьте компоненты по умолчанию или настройте компоненты IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1653">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="c302a-1654">**Проверка подлинности Windows (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c302a-1654">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c302a-1655">Чтобы включить проверку подлинности Windows, разверните такие узлы: **Службы Интернета** > **Безопасность**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1655">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="c302a-1656">Выберите компонент **Проверка подлинности Windows**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1656">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c302a-1657">Дополнительные сведения см. в статьях [Проверка подлинности Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) и [Настройка проверки подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="c302a-1657">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c302a-1658">**Протокол WebSocket (необязательный компонент)**</span><span class="sxs-lookup"><span data-stu-id="c302a-1658">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c302a-1659">Протокол WebSocket поддерживается в ASP.NET Core начиная с версии 1.1.</span><span class="sxs-lookup"><span data-stu-id="c302a-1659">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c302a-1660">Чтобы включить протокол WebSocket, разверните такие узлы: **Службы Интернета** > **Компоненты разработки приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1660">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="c302a-1661">Выберите компонент **Протокол WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1661">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c302a-1662">Дополнительные сведения см. в разделе [Протокол WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="c302a-1662">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c302a-1663">Если во время установки IIS потребуется перезагрузка, перезагрузите систему.</span><span class="sxs-lookup"><span data-stu-id="c302a-1663">If the IIS installation requires a restart, restart the system.</span></span>

![Группы "Консоль управления IIS" и "Службы Интернета" выбраны в окне "Компоненты Windows".](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="c302a-1665">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="c302a-1665">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="c302a-1666">Установите *пакет размещения .NET Core* в размещающей системе.</span><span class="sxs-lookup"><span data-stu-id="c302a-1666">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="c302a-1667">В составе пакета устанавливаются среда выполнения .NET Core, библиотека .NET Core и [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c302a-1667">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="c302a-1668">Модуль позволяет запускать приложения ASP.NET Core под управлением IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1668">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c302a-1669">Если пакет размещения устанавливается до установки служб IIS, его нужно восстановить.</span><span class="sxs-lookup"><span data-stu-id="c302a-1669">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="c302a-1670">После установки служб IIS запустите установщик пакета размещения еще раз.</span><span class="sxs-lookup"><span data-stu-id="c302a-1670">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="c302a-1671">Если пакет размещения устанавливается после установки 64-разрядной (x 64) версии .NET Core, пакеты SDK могут не отображаться (см. раздел [Пакеты SDK .NET Core не обнаружены](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="c302a-1671">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="c302a-1672">Сведения об устранении проблемы см. в статье <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1672">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="c302a-1673">Скачать</span><span class="sxs-lookup"><span data-stu-id="c302a-1673">Download</span></span>

1. <span data-ttu-id="c302a-1674">Перейдите на страницу [загрузки .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="c302a-1674">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="c302a-1675">Выберите требуемую версию .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1675">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="c302a-1676">В столбце **Запуск приложений — среда выполнения** найдите строку, содержащую нужную версию среды выполнения .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1676">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="c302a-1677">Скачайте установщик по ссылке **Hosting Bundle** (Пакет размещения).</span><span class="sxs-lookup"><span data-stu-id="c302a-1677">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="c302a-1678">Некоторые установщики содержат версии выпусков, которые достигли конца своего жизненного цикла и больше не поддерживаются корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="c302a-1678">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="c302a-1679">Дополнительные сведения см. в разделе [Политика поддержки](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="c302a-1679">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="c302a-1680">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="c302a-1680">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="c302a-1681">Запустите установщик на сервере.</span><span class="sxs-lookup"><span data-stu-id="c302a-1681">Run the installer on the server.</span></span> <span data-ttu-id="c302a-1682">При запуске установщика из командной оболочки администратора доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="c302a-1682">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="c302a-1683">`OPT_NO_ANCM=1`. Пропуск установки модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1683">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="c302a-1684">`OPT_NO_RUNTIME=1`. Пропуск установки среды выполнения .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1684">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="c302a-1685">Используется, когда на сервере размещаются только [автономные развертывания](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="c302a-1685">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="c302a-1686">`OPT_NO_SHAREDFX=1`. Пропуск установки общей платформы ASP.NET (среды выполнения ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="c302a-1686">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="c302a-1687">Используется, когда на сервере размещаются только [автономные развертывания](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="c302a-1687">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="c302a-1688">`OPT_NO_X86=1`. Пропуск установки сред выполнения x86.</span><span class="sxs-lookup"><span data-stu-id="c302a-1688">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="c302a-1689">Этот параметр следует использовать, если вы наверняка не будете размещать 32-разрядные приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1689">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="c302a-1690">Если есть хоть малейшая возможность, что в будущем придется размещать и 32-разрядные, и 64-разрядные приложения, не указывайте этот параметр и установите обе среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1690">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="c302a-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`. Отключение проверки использования общей конфигурации IIS, если файл общей конфигурации (*applicationHost.config*) находится на том же компьютере, что и установка IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="c302a-1692">*Доступен только для пакетных установщиков размещения ASP.NET Core 2.2 или более поздней версии.*</span><span class="sxs-lookup"><span data-stu-id="c302a-1692">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="c302a-1693">Для получения дополнительной информации см. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1693">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="c302a-1694">Перезапустите систему или выполните в командной оболочке следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c302a-1694">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="c302a-1695">Перезапуск служб IIS позволит обнаружить внесенные установщиком изменения в системном пути, который является переменной среды.</span><span class="sxs-lookup"><span data-stu-id="c302a-1695">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="c302a-1696">При установке пакета размещения останавливать отдельные сайты служб IIS вручную не нужно.</span><span class="sxs-lookup"><span data-stu-id="c302a-1696">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="c302a-1697">При перезапуске служб IIS осуществляется перезапуск размещенных приложений (сайтов IIS).</span><span class="sxs-lookup"><span data-stu-id="c302a-1697">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="c302a-1698">Приложения запускаются снова при получении первого запроса, в частности от [модуля инициализации приложений](#application-initialization-module-and-idle-timeout).</span><span class="sxs-lookup"><span data-stu-id="c302a-1698">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="c302a-1699">ASP.NET Core наследует поведение наката для выпусков исправлений пакетов общей платформы.</span><span class="sxs-lookup"><span data-stu-id="c302a-1699">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="c302a-1700">Когда приложения, размещенные в службах IIS, перезапускаются вместе с IIS, они загружаются с последними выпусками исправлений связанных пакетов при получении первого запроса.</span><span class="sxs-lookup"><span data-stu-id="c302a-1700">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="c302a-1701">Если службы IIS не перезапускаются, приложения перезапускаются и демонстрируют поведение наката, когда их рабочие процессы перезапускается и они получают первый запрос.</span><span class="sxs-lookup"><span data-stu-id="c302a-1701">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="c302a-1702">Сведения об общей конфигурации IIS см. в разделе [Модуль ASP.NET Core с общей конфигурацией IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="c302a-1702">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="c302a-1703">Установка веб-развертывания при публикации с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c302a-1703">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="c302a-1704">При развертывании приложений на серверах с помощью [веб-развертывания](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) установите на сервере последнюю версию веб-развертывания.</span><span class="sxs-lookup"><span data-stu-id="c302a-1704">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="c302a-1705">Чтобы установить веб-развертывание, можно использовать [установщик веб-платформы (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) или получить установщик непосредственно в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="c302a-1705">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="c302a-1706">Предпочтительный метод — использовать WebPI.</span><span class="sxs-lookup"><span data-stu-id="c302a-1706">The preferred method is to use WebPI.</span></span> <span data-ttu-id="c302a-1707">WebPI обеспечивает автономную установку и настройку поставщиков размещения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1707">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="c302a-1708">Создание сайта IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1708">Create the IIS site</span></span>

1. <span data-ttu-id="c302a-1709">В размещающей системе создайте папку, в которой будут храниться файлы и папки опубликованного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1709">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="c302a-1710">На следующем этапе путь к папке предоставляется IIS как физический путь к приложению.</span><span class="sxs-lookup"><span data-stu-id="c302a-1710">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="c302a-1711">Дополнительные сведения о папке развертывания и структуре файлов приложения см. в статье <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1711">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="c302a-1712">В окне диспетчера IIS на панели **Подключения** разверните узел сервера.</span><span class="sxs-lookup"><span data-stu-id="c302a-1712">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="c302a-1713">Щелкните правой кнопкой мыши папку **Сайты**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1713">Right-click the **Sites** folder.</span></span> <span data-ttu-id="c302a-1714">В контекстном меню выберите пункт **Добавить веб-сайт**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1714">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="c302a-1715">Укажите имя в поле **Имя сайта** и задайте значение в поле **Физический путь** для созданной папки развертывания приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1715">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="c302a-1716">Укажите конфигурацию **привязки** и нажмите кнопку **ОК**, чтобы создать веб-сайт.</span><span class="sxs-lookup"><span data-stu-id="c302a-1716">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![В окне "Добавить веб-сайт" укажите имя сайта, физический путь и имя узла.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="c302a-1718">**Не используйте** привязки с подстановочными знаками (`http://*:80/` и `http://+:80`) на верхнем уровне.</span><span class="sxs-lookup"><span data-stu-id="c302a-1718">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="c302a-1719">Это может создать уязвимость и поставить ваше приложение под угрозу.</span><span class="sxs-lookup"><span data-stu-id="c302a-1719">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="c302a-1720">Сюда относятся и строгие, и нестрогие подстановочные знаки.</span><span class="sxs-lookup"><span data-stu-id="c302a-1720">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="c302a-1721">Вместо этого используйте имена узлов в явном виде.</span><span class="sxs-lookup"><span data-stu-id="c302a-1721">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="c302a-1722">Привязки с подстановочными знаками на уровне дочерних доменов (например `*.mysub.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость).</span><span class="sxs-lookup"><span data-stu-id="c302a-1722">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="c302a-1723">Дополнительные сведения см. в документе [rfc7230, раздел 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="c302a-1723">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="c302a-1724">Разверните узел сервера и выберите **Пулы приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1724">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="c302a-1725">Щелкните правой кнопкой мыши пул приложений сайта и в контекстном меню выберите пункт **Основные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1725">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="c302a-1726">В окне **Изменение пула приложений** задайте для параметра **Версия среды CLR .NET** значение **Без управляемого кода**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1726">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Выбор значения "Без управляемого кода" для параметра "Версия среды CLR .NET".](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="c302a-1728">ASP.NET Core выполняется в отдельном процессе и управляет средой выполнения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1728">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="c302a-1729">ASP.NET Core не зависит от загрузки среды CLR для ПК (.NET CLR) &mdash; для размещения приложения в рабочем процессе запускается CoreCLR для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1729">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="c302a-1730">Задавать для параметра **Версия среды CLR .NET** значение **Без управляемого кода** необязательно, но рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="c302a-1730">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="c302a-1731">*ASP.NET Core 2.2 или более поздней версии*: для 64-разрядного (x64) [автономного развертывания](/dotnet/core/deploying/#self-contained-deployments-scd), в котором используется [модель размещения в процессе](#in-process-hosting-model), отключите пул приложений для 32-разрядных (x86) процессов.</span><span class="sxs-lookup"><span data-stu-id="c302a-1731">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="c302a-1732">На боковой панели **Действия** в разделе **Пулы приложений** диспетчера IIS выберите **Задать значения по умолчанию для пула приложений** или **Дополнительные параметры**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1732">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="c302a-1733">Найдите пункт **Включить 32-разрядные приложения** и задайте значение `False`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1733">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="c302a-1734">Этот параметр не влияет на приложения, развернутые для [размещения вне процесса](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="c302a-1734">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="c302a-1735">Убедитесь в том, что удостоверение модели процесса имеет соответствующие разрешения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1735">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="c302a-1736">При изменении удостоверения по умолчанию для пула приложений (**Модель процесса** >  **Identity** ) с **ApplicationPoolIdentity** на другое, убедитесь, что у нового удостоверения есть соответствующие разрешения для доступа к папке приложения, базе данных и другим необходимым ресурсам.</span><span class="sxs-lookup"><span data-stu-id="c302a-1736">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="c302a-1737">Например, пулу приложений требуются права на чтение и запись в папках, в которых приложение считывает и записывает файлы.</span><span class="sxs-lookup"><span data-stu-id="c302a-1737">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="c302a-1738">**Настройка проверки подлинности Windows (необязательный этап)**</span><span class="sxs-lookup"><span data-stu-id="c302a-1738">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="c302a-1739">См. статью [Configure Windows authentication in an ASP.NET Core app](xref:security/authentication/windowsauth) (Настройка проверки подлинности Windows в приложении ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="c302a-1739">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="c302a-1740">Развертывание приложения</span><span class="sxs-lookup"><span data-stu-id="c302a-1740">Deploy the app</span></span>

<span data-ttu-id="c302a-1741">Разверните приложение в папке IIS, **физический путь** к которой вы указали в рамках раздела [Создание сайта IIS](#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="c302a-1741">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="c302a-1742">Рекомендуется выполнять [веб-развертывание](/iis/publish/using-web-deploy/introduction-to-web-deploy), но есть несколько других способов переместить приложение из папки *публикации* проекта в папку развертывания размещающей системы.</span><span class="sxs-lookup"><span data-stu-id="c302a-1742">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="c302a-1743">Веб-развертывание с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c302a-1743">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="c302a-1744">Сведения о создании профиля публикации для веб-развертывания см. в статье [Профили публикации Visual Studio для развертывания приложений ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="c302a-1744">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="c302a-1745">Если поставщик услуг размещения предоставляет профиль публикации или позволяет его создать, скачайте этот профиль и импортируйте его с помощью диалогового окна **Публикация** в Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c302a-1745">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Диалоговое окно "Публикация"](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="c302a-1747">Веб-развертывание вне Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c302a-1747">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="c302a-1748">[Веб-развертывание](/iis/publish/using-web-deploy/introduction-to-web-deploy) можно также использовать вне Visual Studio с помощью командной строки.</span><span class="sxs-lookup"><span data-stu-id="c302a-1748">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="c302a-1749">Дополнительные сведения см. в статье [Средство веб-развертывания](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="c302a-1749">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="c302a-1750">Альтернативы веб-развертыванию</span><span class="sxs-lookup"><span data-stu-id="c302a-1750">Alternatives to Web Deploy</span></span>

<span data-ttu-id="c302a-1751">Переместить приложение в размещающую систему можно несколькими способами: с помощью копирования вручную, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) или [PowerShell](/powershell/).</span><span class="sxs-lookup"><span data-stu-id="c302a-1751">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="c302a-1752">Дополнительные сведения о развертывании ASP.NET Core в службах IIS см. в разделе [Ресурсы развертывания для администраторов IIS](#deployment-resources-for-iis-administrators).</span><span class="sxs-lookup"><span data-stu-id="c302a-1752">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="c302a-1753">Обзор веб-сайта</span><span class="sxs-lookup"><span data-stu-id="c302a-1753">Browse the website</span></span>

<span data-ttu-id="c302a-1754">Когда приложение будет развернуто в размещающей системе, выполните запрос к одной из общедоступных конечных точек приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1754">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="c302a-1755">В приведенном ниже примере сайт привязан к **имени узла** IIS `www.mysite.com` в **порте** `80`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1755">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="c302a-1756">Запрос отправляется по адресу `http://www.mysite.com`:</span><span class="sxs-lookup"><span data-stu-id="c302a-1756">A request is made to `http://www.mysite.com`:</span></span>

![Начальная страница IIS, загруженная в браузере Microsoft Edge.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="c302a-1758">Заблокированные файлы развертывания</span><span class="sxs-lookup"><span data-stu-id="c302a-1758">Locked deployment files</span></span>

<span data-ttu-id="c302a-1759">Во время выполнения приложения файлы в папке развертывания блокируются.</span><span class="sxs-lookup"><span data-stu-id="c302a-1759">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="c302a-1760">Заблокированные файлы невозможно перезаписать во время развертывания.</span><span class="sxs-lookup"><span data-stu-id="c302a-1760">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="c302a-1761">Чтобы снять блокировку с файлов в развертывании, остановите пул приложений с помощью **одного** из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="c302a-1761">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="c302a-1762">Запустите веб-развертывание и добавьте ссылку на `Microsoft.NET.Sdk.Web` в файл проекта.</span><span class="sxs-lookup"><span data-stu-id="c302a-1762">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="c302a-1763">Файл *app_offline.htm* помещается в корень каталога веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1763">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="c302a-1764">Если файл присутствует, модуль ASP.NET Core корректно завершает работу приложения и обслуживает файл *app_offline.htm* во время развертывания.</span><span class="sxs-lookup"><span data-stu-id="c302a-1764">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="c302a-1765">Дополнительные сведения см. в разделе [Справочник по конфигурации модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="c302a-1765">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="c302a-1766">Вручную остановите пул приложений в диспетчере служб IIS на сервере.</span><span class="sxs-lookup"><span data-stu-id="c302a-1766">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="c302a-1767">С помощью PowerShell удалите *app_offline.htm* (требуется PowerShell 5 или более поздняя версия):</span><span class="sxs-lookup"><span data-stu-id="c302a-1767">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="c302a-1768">Защита данных</span><span class="sxs-lookup"><span data-stu-id="c302a-1768">Data protection</span></span>

<span data-ttu-id="c302a-1769">[Стек защиты данных ASP.NET Core](xref:security/data-protection/introduction) используют несколько [ПО промежуточного слоя](xref:fundamentals/middleware/index) ASP.NET Core, включая ПО, которое применяется для аутентификации.</span><span class="sxs-lookup"><span data-stu-id="c302a-1769">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="c302a-1770">Даже если API-интерфейсы защиты данных не вызываются из пользовательского кода, защиту данных следует настроить для создания постоянного [хранилища криптографических ключей](xref:security/data-protection/implementation/key-management). Это можно сделать с помощью скрипта развертывания или в пользовательском коде.</span><span class="sxs-lookup"><span data-stu-id="c302a-1770">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="c302a-1771">Если защита данных не настроена, ключи хранятся в памяти и удаляются при перезапуске приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1771">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="c302a-1772">Если набор ключей хранится в памяти, при перезапуске приложения происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="c302a-1772">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="c302a-1773">Все токены аутентификации, использующие файлы cookie, становятся недействительными.</span><span class="sxs-lookup"><span data-stu-id="c302a-1773">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="c302a-1774">При выполнении следующего запроса пользователю требуется выполнить вход снова.</span><span class="sxs-lookup"><span data-stu-id="c302a-1774">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="c302a-1775">Все данные, защищенные с помощью набора ключей, больше не могут быть расшифрованы.</span><span class="sxs-lookup"><span data-stu-id="c302a-1775">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="c302a-1776">Это могут быть [токены CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) и [файлы cookie временных данных ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="c302a-1776">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="c302a-1777">Чтобы настроить защиту данных в службах IIS для хранения набора ключей, воспользуйтесь **одним** из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="c302a-1777">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="c302a-1778">**Создание раздела реестра для защиты данных**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1778">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="c302a-1779">Ключи защиты данных, используемые приложениями ASP.NET Core, хранятся во внешнем для приложений реестре.</span><span class="sxs-lookup"><span data-stu-id="c302a-1779">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="c302a-1780">Чтобы хранить эти ключи для определенного приложения, нужно создать разделы реестра для пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-1780">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="c302a-1781">При автономной установке служб IIS, не поддерживающей веб-ферму, можно выполнить [скрипт PowerShell Provision-AutoGenKeys.ps1 для защиты данных](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) применительно к каждому пулу приложений, в который входит приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1781">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="c302a-1782">Этот скрипт создает раздел в реестре HKLM, который будет доступен только для учетной записи рабочего процесса пула приложений, к которому относится соответствующее приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-1782">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="c302a-1783">Неактивные ключи шифруются с помощью API защиты данных с ключом компьютера.</span><span class="sxs-lookup"><span data-stu-id="c302a-1783">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="c302a-1784">В случаях, когда используется веб-ферма, в приложении можно настроить UNC-путь, по которому это приложение будет хранить набор ключей для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="c302a-1784">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="c302a-1785">По умолчанию ключи защиты данных не шифруются.</span><span class="sxs-lookup"><span data-stu-id="c302a-1785">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="c302a-1786">Разрешения на доступ к файлам в сетевой папке должны быть предоставлены только учетной записи Windows, с помощью которой выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-1786">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="c302a-1787">Для защиты неактивных ключей можно использовать сертификат X509.</span><span class="sxs-lookup"><span data-stu-id="c302a-1787">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="c302a-1788">Рассмотрите возможность реализации механизма, позволяющего пользователям отправлять сертификаты: поместите сертификаты в хранилище доверенных сертификатов пользователя и обеспечьте их доступность на всех компьютерах, где выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-1788">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="c302a-1789">Дополнительные сведения см. в разделе [Настройка защиты данных в ASP.NET Core](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="c302a-1789">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="c302a-1790">**Настройка пула приложений IIS для загрузки профиля пользователя**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1790">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="c302a-1791">Этот параметр находится на странице **Дополнительные параметры** пула приложений в разделе **Модель процесса**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1791">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="c302a-1792">Задайте для параметра **Загрузить профиль пользователя** значение `True`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1792">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="c302a-1793">Если задать значение `True`, ключи будут храниться в каталоге профиля пользователя и защищаться с помощью API защиты данных и ключа на уровне учетной записи пользователя.</span><span class="sxs-lookup"><span data-stu-id="c302a-1793">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="c302a-1794">Ключи хранятся в папке *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="c302a-1794">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="c302a-1795">Также необходимо включить атрибут [setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-1795">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="c302a-1796">Значением свойства `setProfileEnvironment` по умолчанию является `true`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1796">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="c302a-1797">В некоторых сценариях (например, в ОС Windows) для параметра `setProfileEnvironment` установлено значение `false`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1797">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="c302a-1798">Если ключи не хранятся в каталоге профиля пользователя:</span><span class="sxs-lookup"><span data-stu-id="c302a-1798">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="c302a-1799">Перейдите к папке *%windir%/system32/inetsrv/config*.</span><span class="sxs-lookup"><span data-stu-id="c302a-1799">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="c302a-1800">Откройте файл *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="c302a-1800">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="c302a-1801">Найдите элемент `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .</span><span class="sxs-lookup"><span data-stu-id="c302a-1801">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="c302a-1802">Убедитесь, что атрибут `setProfileEnvironment` отсутствует и установлено значение по умолчанию `true`, или же явно задайте для атрибута значение `true`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1802">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="c302a-1803">**Использование файловой системы в качестве хранилища набора ключей**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1803">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="c302a-1804">Измените код приложения так, чтобы [в качестве хранилища набора ключей использовалась файловая система](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="c302a-1804">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="c302a-1805">Для защиты набора ключей используйте доверенный сертификат X509.</span><span class="sxs-lookup"><span data-stu-id="c302a-1805">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="c302a-1806">Если сертификат — самозаверяющий, поместите его в доверенное корневое хранилище.</span><span class="sxs-lookup"><span data-stu-id="c302a-1806">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="c302a-1807">При использовании служб IIS в веб-ферме:</span><span class="sxs-lookup"><span data-stu-id="c302a-1807">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="c302a-1808">используйте общую папку, которая доступна всем компьютерам;</span><span class="sxs-lookup"><span data-stu-id="c302a-1808">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="c302a-1809">разверните сертификат X509 на каждом компьютере;</span><span class="sxs-lookup"><span data-stu-id="c302a-1809">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="c302a-1810">настройте [защиту данных в коде](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="c302a-1810">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="c302a-1811">**Настройка политики защиты данных на уровне компьютера**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1811">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="c302a-1812">Система защиты данных обеспечивает ограниченную поддержку задания [политики по умолчанию на уровне компьютера](xref:security/data-protection/configuration/machine-wide-policy) для всех приложений, использующих интерфейсы API защиты данных.</span><span class="sxs-lookup"><span data-stu-id="c302a-1812">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="c302a-1813">Для получения дополнительной информации см. <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1813">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="c302a-1814">Виртуальные каталоги</span><span class="sxs-lookup"><span data-stu-id="c302a-1814">Virtual Directories</span></span>

<span data-ttu-id="c302a-1815">[Виртуальные каталоги IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) не поддерживаются в приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1815">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="c302a-1816">Приложение может размещаться как [вложенное](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="c302a-1816">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="c302a-1817">Вложенные приложения</span><span class="sxs-lookup"><span data-stu-id="c302a-1817">Sub-applications</span></span>

<span data-ttu-id="c302a-1818">Приложение ASP.NET Core можно разместить как [вложенное приложение IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="c302a-1818">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="c302a-1819">Путь к вложенному приложению становится частью URL-адреса главного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1819">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="c302a-1820">Вложенное приложение не должно включать модуль ASP.NET Core в качестве обработчика.</span><span class="sxs-lookup"><span data-stu-id="c302a-1820">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="c302a-1821">Если модуль добавляется в качестве обработчика в файл *web.config* дочернего приложения, при попытке работы с этим приложением выводится ошибка *500.19* (внутренняя ошибка сервера) с указанием некорректного файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="c302a-1821">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="c302a-1822">Вот пример опубликованного файла *web.config* для дочернего приложения ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c302a-1822">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="c302a-1823">Размещая вложенное приложение не на основе ASP.NET Core в приложении ASP.NET Core, явным образом удалите унаследованный обработчик из файла *web.config* вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1823">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="c302a-1824">В ссылках на статический ресурс во вложенном приложении следует использовать нотацию `~/`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1824">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="c302a-1825">Такая нотация активирует [вспомогательную функцию тега](xref:mvc/views/tag-helpers/intro) для добавления свойства pathbase вложенного приложения в начало отображаемой относительной ссылки.</span><span class="sxs-lookup"><span data-stu-id="c302a-1825">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="c302a-1826">Для вложенного приложения с путем `/subapp_path` ссылка на изображение `src="~/image.png"` отображается как `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1826">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="c302a-1827">ПО промежуточного слоя для обработки статических файлов в главном приложении не обрабатывает такой запрос статического файла.</span><span class="sxs-lookup"><span data-stu-id="c302a-1827">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="c302a-1828">Запрос обрабатывается соответствующим ПО вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1828">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="c302a-1829">Если атрибуту `src` статического ресурса присваивается абсолютный путь (например, `src="/image.png"`), ссылка отображается без свойства pathbase вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1829">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="c302a-1830">ПО промежуточного слоя для обработки статических файлов в главном приложении пытается найти ресурс в [корневом веб-каталоге](xref:fundamentals/index#web-root) главного приложения, что приводит к ошибке *404 — Not Found* (не найдено), кроме случаев, когда статический ресурс доступен из главного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1830">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="c302a-1831">Для размещения приложения ASP.NET Core в качестве приложения, вложенного в другое приложение ASP.NET Core, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="c302a-1831">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="c302a-1832">Создайте пул приложений для вложенного приложения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1832">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="c302a-1833">Установите для параметра **Версия среды CLR .NET** значение **Без управляемого кода**, так как для размещения приложения в рабочем процессе запускается CoreCLR для .NET Core, а не среда CRL для настольных ПК (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="c302a-1833">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="c302a-1834">Добавьте корневой веб-сайт в диспетчере служб IIS с вложенным приложением в папке внутри корневого веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="c302a-1834">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="c302a-1835">Щелкните правой кнопкой мыши папку вложенного приложения в диспетчере служб IIS и выберите **Преобразовать в приложение**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1835">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="c302a-1836">В диалоговом окне **Добавление приложения** нажмите кнопку **Выбрать**, чтобы назначить созданный **пул приложений** вложенному приложению.</span><span class="sxs-lookup"><span data-stu-id="c302a-1836">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="c302a-1837">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1837">Select **OK**.</span></span>

<span data-ttu-id="c302a-1838">Назначение отдельного пула приложений вложенному приложению является обязательным при использовании модели внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1838">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="c302a-1839">Дополнительные сведения о внутрипроцессной модели размещения и настройке модуля ASP.NET Core см. в статье <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="c302a-1839">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="c302a-1840">Настройка служб IIS с помощью файла web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-1840">Configuration of IIS with web.config</span></span>

<span data-ttu-id="c302a-1841">Конфигурация IIS зависит от `<system.webServer>` раздела *web.config* для сценариев IIS, предназначенных для работы приложений ASP.NET Core с помощью модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1841">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="c302a-1842">Например, конфигурация IIS работает для динамического сжатия.</span><span class="sxs-lookup"><span data-stu-id="c302a-1842">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="c302a-1843">Если в службах IIS на уровне сервера настроено динамическое сжатие, элемент `<urlCompression>` в файле *web.config* приложения может отключить это сжатие для приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1843">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="c302a-1844">Дополнительные сведения см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="c302a-1844">For more information, see the following topics:</span></span>

* [<span data-ttu-id="c302a-1845">Справочник по настройке для \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="c302a-1845">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="c302a-1846">Сведения о настройке переменных среды для отдельных приложений, выполняющихся в изолированных пулах приложений (такая возможность поддерживается в службах IIS начиная с версии 10.0), см. в справочной документации по службам IIS, в частности в разделе *Команда AppCmd.exe* статьи [Переменные среды \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="c302a-1846">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="c302a-1847">Разделы конфигурации файла web.config</span><span class="sxs-lookup"><span data-stu-id="c302a-1847">Configuration sections of web.config</span></span>

<span data-ttu-id="c302a-1848">Разделы конфигурации приложений ASP.NET 4.x в файле *web.config* не используются для конфигурации приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c302a-1848">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="c302a-1849">Для настройки приложений ASP.NET Core используются другие поставщики конфигураций.</span><span class="sxs-lookup"><span data-stu-id="c302a-1849">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="c302a-1850">Дополнительные сведения см. в разделе [Конфигурация](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c302a-1850">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="c302a-1851">Пулы приложений</span><span class="sxs-lookup"><span data-stu-id="c302a-1851">Application Pools</span></span>

<span data-ttu-id="c302a-1852">При размещении на сервере множества веб-сайтов рекомендуем изолировать приложения друг от друга, запуская каждое из них в собственном пуле.</span><span class="sxs-lookup"><span data-stu-id="c302a-1852">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="c302a-1853">В диалоговом окне **Добавить веб-сайт** служб IIS такая конфигурация задана по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c302a-1853">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="c302a-1854">Если указано **Имя сайта**, оно автоматически переносится в текстовое поле **Пул приложений**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1854">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="c302a-1855">При добавлении веб-сайта создается пул приложений с именем сайта.</span><span class="sxs-lookup"><span data-stu-id="c302a-1855">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="c302a-1856">Identity пула приложений</span><span class="sxs-lookup"><span data-stu-id="c302a-1856">Application Pool Identity</span></span>

<span data-ttu-id="c302a-1857">Учетная запись удостоверения пула приложений позволяет запускать приложение от имени уникальной учетной записи, не создавая домены или локальные учетные записи и не управляя ими.</span><span class="sxs-lookup"><span data-stu-id="c302a-1857">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="c302a-1858">В службах IIS начиная с версии 8.0 рабочий процесс администратора IIS (WAS) создает виртуальную учетную запись с именем нового пула приложений и по умолчанию выполняет рабочие процессы пула приложений с помощью этой учетной записи.</span><span class="sxs-lookup"><span data-stu-id="c302a-1858">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="c302a-1859">В консоли управления IIS в окне **Дополнительные параметры** пула приложений для **Identity** необходимо выбрать **ApplicationPoolIdentity**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1859">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Диалоговое окно дополнительных параметров для пула приложений](index/_static/apppool-identity.png)

<span data-ttu-id="c302a-1861">Процесс управления IIS создает в системе безопасности Windows безопасный идентификатор с именем пула приложений.</span><span class="sxs-lookup"><span data-stu-id="c302a-1861">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="c302a-1862">С помощью этого идентификатора можно защищать ресурсы,</span><span class="sxs-lookup"><span data-stu-id="c302a-1862">Resources can be secured using this identity.</span></span> <span data-ttu-id="c302a-1863">но он не является реальной учетной записью и не отображается в консоли управления пользователями Windows.</span><span class="sxs-lookup"><span data-stu-id="c302a-1863">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="c302a-1864">Если рабочему процессу IIS требуется предоставить доступ к приложению с повышенными правами, измените список управления доступом (ACL) для каталога, содержащего приложение.</span><span class="sxs-lookup"><span data-stu-id="c302a-1864">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="c302a-1865">Откройте проводник и перейдите к каталогу.</span><span class="sxs-lookup"><span data-stu-id="c302a-1865">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="c302a-1866">Щелкните каталог правой кнопкой мыши и выберите пункт **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1866">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="c302a-1867">На вкладке **Безопасность** нажмите кнопку **Изменить**, а затем — кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1867">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="c302a-1868">Нажмите кнопку **Размещение** и выберите систему.</span><span class="sxs-lookup"><span data-stu-id="c302a-1868">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="c302a-1869">В поле **Введите имена выбираемых объектов** введите **IIS AppPool\\<имя_пула_приложений>** .</span><span class="sxs-lookup"><span data-stu-id="c302a-1869">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="c302a-1870">Нажмите кнопку **Проверить имена**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1870">Select the **Check Names** button.</span></span> <span data-ttu-id="c302a-1871">В случае с *DefaultAppPool* проверьте имена с помощью **IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1871">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="c302a-1872">После нажатия кнопки **Проверить имена** в области имен объектов отобразится значение **DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1872">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="c302a-1873">Вручную ввести имя пула приложений в области имен объектов нельзя.</span><span class="sxs-lookup"><span data-stu-id="c302a-1873">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="c302a-1874">При поиске имени объекта используйте формат **IIS AppPool\\<имя_пула_приложений>** .</span><span class="sxs-lookup"><span data-stu-id="c302a-1874">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Диалоговое окно "Выбор пользователей или групп" для папки приложения. До нажатия кнопки "Проверить имена" в области имен объектов к строке IIS AppPool\" добавилось имя пула приложений, DefaultAppPool.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="c302a-1876">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1876">Select **OK**.</span></span>

   ![Диалоговое окно "Выбор пользователей или групп" для папки приложения. После нажатия кнопки "Проверить имена" в области имен объектов отображается имя пула приложений, DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="c302a-1878">Разрешения на чтение и выполнение предоставляются по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c302a-1878">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="c302a-1879">При необходимости предоставьте дополнительные разрешения.</span><span class="sxs-lookup"><span data-stu-id="c302a-1879">Provide additional permissions as needed.</span></span>

<span data-ttu-id="c302a-1880">Кроме того, доступ можно предоставить через командную строку, используя средство **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="c302a-1880">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="c302a-1881">В случае с *DefaultAppPool* выполняется такая команда:</span><span class="sxs-lookup"><span data-stu-id="c302a-1881">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="c302a-1882">Дополнительные сведения об ICACLS см. [здесь](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="c302a-1882">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="c302a-1883">Поддержка HTTP/2</span><span class="sxs-lookup"><span data-stu-id="c302a-1883">HTTP/2 support</span></span>

<span data-ttu-id="c302a-1884">[HTTP/2](https://httpwg.org/specs/rfc7540.html) поддерживается для внепроцессных развертываний, которые удовлетворяют следующим базовым требованиям:</span><span class="sxs-lookup"><span data-stu-id="c302a-1884">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="c302a-1885">Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="c302a-1885">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="c302a-1886">Подключения к пограничным серверам, открытых для общего доступа, выполняются по протоколу HTTP/2, а подключения к [серверу Kestrel](xref:fundamentals/servers/kestrel) через обратный прокси-сервер — по HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c302a-1886">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="c302a-1887">Целевая платформа: неприменимо к развертываниям вне процесса, так как IIS полностью обрабатывает подключение HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c302a-1887">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="c302a-1888">Подключение TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="c302a-1888">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c302a-1889">Если установлено подключение HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="c302a-1889">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="c302a-1890">Протокол HTTP/2 по умолчанию включен.</span><span class="sxs-lookup"><span data-stu-id="c302a-1890">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="c302a-1891">Если не удается установить подключение HTTP/2, применяется резервный вариант HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c302a-1891">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="c302a-1892">Дополнительные сведения о настройке HTTP/2 для развертываний IIS см. в статье [об HTTP/2 в IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="c302a-1892">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="c302a-1893">Предварительные запросы CORS</span><span class="sxs-lookup"><span data-stu-id="c302a-1893">CORS preflight requests</span></span>

<span data-ttu-id="c302a-1894">*Этот раздел относится только к приложениям ASP.NET Core, предназначенным для .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="c302a-1894">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="c302a-1895">Для приложения ASP.NET Core, предназначенного для .NET Framework, параметры OPTIONS не передаются в приложение по умолчанию в службах IIS.</span><span class="sxs-lookup"><span data-stu-id="c302a-1895">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="c302a-1896">Чтобы узнать, как настроить обработчики приложения IIS в файле *web.config* для передачи запросов OPTIONS, см. раздел [Enable cross-origin requests in ASP.NET Web API 2. How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works) (Включение запросов CORS в ASP.NET Web API 2. Принцип работы CORS).</span><span class="sxs-lookup"><span data-stu-id="c302a-1896">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="c302a-1897">Ресурсы развертывания для администраторов IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1897">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="c302a-1898">Документация по службам IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1898">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="c302a-1899">Начало работы с диспетчером IIS в IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1899">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="c302a-1900">Развертывание приложений .NET Core</span><span class="sxs-lookup"><span data-stu-id="c302a-1900">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="c302a-1901">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c302a-1901">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="c302a-1902">Официальный веб-сайт Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1902">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="c302a-1903">Библиотека технического содержимого по Windows Server</span><span class="sxs-lookup"><span data-stu-id="c302a-1903">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="c302a-1904">HTTP/2 в IIS</span><span class="sxs-lookup"><span data-stu-id="c302a-1904">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
