---
title: Ведение журнала и диагностика в gRPC на платформе .NET
author: jamesnk
description: Сведения о сборе диагностики из приложения gRPC на платформе .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/diagnostics
ms.openlocfilehash: ca87ba5b03e7657132e5ebaba72370bde85c85d4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406775"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="8da7e-103">Ведение журнала и диагностика в gRPC на платформе .NET</span><span class="sxs-lookup"><span data-stu-id="8da7e-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="8da7e-104">Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)</span><span class="sxs-lookup"><span data-stu-id="8da7e-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="8da7e-105">В этой статье приводятся рекомендации по сбору диагностических данных из приложения gRPC с целью устранения неполадок.</span><span class="sxs-lookup"><span data-stu-id="8da7e-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="8da7e-106">Здесь рассматриваются такие темы:</span><span class="sxs-lookup"><span data-stu-id="8da7e-106">Topics covered include:</span></span>

* <span data-ttu-id="8da7e-107">**Ведение журналов** — структурированные журналы, записываемые в [систему ведения журналов .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="8da7e-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8da7e-108">Интерфейс <xref:Microsoft.Extensions.Logging.ILogger> используется платформами приложений для записи журналов, а пользователями — для ведения собственных журналов в приложении.</span><span class="sxs-lookup"><span data-stu-id="8da7e-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="8da7e-109">**Трассировка** — события, которые связаны с операцией, записанной с помощью `DiaganosticSource` и `Activity`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="8da7e-110">Трассировки из источника диагностических данных обычно используются для сбора данных телеметрии приложений такими библиотеками, как [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) и [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span><span class="sxs-lookup"><span data-stu-id="8da7e-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="8da7e-111">**Метрики** — представление мер данных за интервалы времени, например количество запросов в секунду.</span><span class="sxs-lookup"><span data-stu-id="8da7e-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="8da7e-112">Метрики создаются с помощью `EventCounter` и могут отслеживаться с помощью программы командной строки [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) или [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="8da7e-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="8da7e-113">Logging</span><span class="sxs-lookup"><span data-stu-id="8da7e-113">Logging</span></span>

<span data-ttu-id="8da7e-114">Службы gRPC и клиент gRPC записывают журналы с помощью [системы ведения журналов .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="8da7e-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8da7e-115">Журналы являются хорошей отправной точкой, когда необходимо отладить код приложения, выполняющийся непредвиденным образом.</span><span class="sxs-lookup"><span data-stu-id="8da7e-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="8da7e-116">Ведение журналов служб gRPC</span><span class="sxs-lookup"><span data-stu-id="8da7e-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="8da7e-117">Журналы на стороне сервера могут содержать конфиденциальные сведения из приложения.</span><span class="sxs-lookup"><span data-stu-id="8da7e-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="8da7e-118">**Никогда**  не публикуйте необработанные журналы из рабочих приложений на общедоступных форумах, таких как GitHub.</span><span class="sxs-lookup"><span data-stu-id="8da7e-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="8da7e-119">Так как службы gRPC размещаются на платформе ASP.NET Core, они используют систему ведения журналов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8da7e-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="8da7e-120">В конфигурации по умолчанию gRPC записывает весьма ограниченное количество сведений, но это можно настроить.</span><span class="sxs-lookup"><span data-stu-id="8da7e-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="8da7e-121">Подробные сведения о настройке ведения журналов в ASP.NET Core см. в документации по [ведению журналов в ASP.NET Core](xref:fundamentals/logging/index#configuration).</span><span class="sxs-lookup"><span data-stu-id="8da7e-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="8da7e-122">gRPC добавляет журналы в категорию `Grpc`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="8da7e-123">Чтобы включить подробные журналы от gRPC, настройте префиксы `Grpc` с уровнем `Debug` в файле *appsettings.json*, добавив следующие элементы в подраздел `LogLevel` раздела `Logging`:</span><span class="sxs-lookup"><span data-stu-id="8da7e-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="8da7e-124">Эту настройку также можно произвести в файле *Startup.cs* с помощью метода `ConfigureLogging`:</span><span class="sxs-lookup"><span data-stu-id="8da7e-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="8da7e-125">Если вы не используете конфигурацию на основе JSON, задайте следующее значение конфигурации в системе конфигурации:</span><span class="sxs-lookup"><span data-stu-id="8da7e-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="8da7e-126">Сведения о задании вложенных значений конфигурации см. в документации по своей системе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="8da7e-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="8da7e-127">Так, при использовании переменных среды вместо символа `:` применяются два символа `_` (например, `Logging__LogLevel__Grpc`).</span><span class="sxs-lookup"><span data-stu-id="8da7e-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="8da7e-128">При сборе подробных диагностических данных для приложения мы рекомендуем использовать уровень `Debug`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="8da7e-129">На уровне `Trace` создаются очень подробные диагностические данные, которые редко требуются для диагностики проблем в приложении.</span><span class="sxs-lookup"><span data-stu-id="8da7e-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="8da7e-130">Пример выходных данных ведения журнала</span><span class="sxs-lookup"><span data-stu-id="8da7e-130">Sample logging output</span></span>

<span data-ttu-id="8da7e-131">Вот пример выходных данных консоли для службы gRPC на уровне `Debug`:</span><span class="sxs-lookup"><span data-stu-id="8da7e-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a><span data-ttu-id="8da7e-132">Доступ к журналам на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="8da7e-132">Access server-side logs</span></span>

<span data-ttu-id="8da7e-133">Способ доступа к журналам на стороне сервера зависит от среды, в которой выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="8da7e-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="8da7e-134">Консольное приложение</span><span class="sxs-lookup"><span data-stu-id="8da7e-134">As a console app</span></span>

<span data-ttu-id="8da7e-135">В консольном приложении по умолчанию должно быть включено [средство ведения журнала консоли](xref:fundamentals/logging/index#console).</span><span class="sxs-lookup"><span data-stu-id="8da7e-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="8da7e-136">Журналы gRPC будут выводиться в консоли.</span><span class="sxs-lookup"><span data-stu-id="8da7e-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="8da7e-137">Другие среды</span><span class="sxs-lookup"><span data-stu-id="8da7e-137">Other environments</span></span>

<span data-ttu-id="8da7e-138">Если приложение развернуто в другой среде (например, Docker, Kubernetes или службе Windows), обратитесь к статье <xref:fundamentals/logging/index> за дополнительными сведениями о настройке подходящих поставщиков ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="8da7e-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="8da7e-139">Ведение журналов клиента gRPC</span><span class="sxs-lookup"><span data-stu-id="8da7e-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="8da7e-140">Журналы на стороне клиента могут содержать конфиденциальные сведения из приложения.</span><span class="sxs-lookup"><span data-stu-id="8da7e-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="8da7e-141">**Никогда**  не публикуйте необработанные журналы из рабочих приложений на общедоступных форумах, таких как GitHub.</span><span class="sxs-lookup"><span data-stu-id="8da7e-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="8da7e-142">Чтобы получать журналы из клиента .NET, можно задать свойство `GrpcChannelOptions.LoggerFactory` при создании канала клиента.</span><span class="sxs-lookup"><span data-stu-id="8da7e-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="8da7e-143">Если служба gRPC вызывается из приложения ASP.NET Core, производство средства ведения журнала может разрешаться посредством внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="8da7e-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="8da7e-144">Другой способ включить ведение журналов клиента — использовать [производство клиента gRPC](xref:grpc/clientfactory) для создания клиента.</span><span class="sxs-lookup"><span data-stu-id="8da7e-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="8da7e-145">Клиент gRPC, зарегистрированный в производстве клиента и разрешенный посредством внедрения зависимостей, будет автоматически использовать настроенные для приложения параметры ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="8da7e-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="8da7e-146">Если приложение не использует внедрение зависимостей, вы можете создать экземпляр `ILoggerFactory` с помощью метода [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="8da7e-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="8da7e-147">Для доступа к этому методу добавьте в приложение пакет [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="8da7e-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="8da7e-148">Области журналов клиента gRPC</span><span class="sxs-lookup"><span data-stu-id="8da7e-148">gRPC client log scopes</span></span>

<span data-ttu-id="8da7e-149">Клиент gRPC добавляет [область ведения журналов](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) для журналов, создаваемых во время вызова gRPC.</span><span class="sxs-lookup"><span data-stu-id="8da7e-149">The gRPC client adds a [logging scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="8da7e-150">Область включает в себя метаданные, связанные с вызовом gRPC.</span><span class="sxs-lookup"><span data-stu-id="8da7e-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="8da7e-151">**GrpcMethodType** — тип метода gRPC.</span><span class="sxs-lookup"><span data-stu-id="8da7e-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="8da7e-152">Возможными значениями являются имена из перечисления `Grpc.Core.MethodType`, например Unary.</span><span class="sxs-lookup"><span data-stu-id="8da7e-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="8da7e-153">**GrpcUri** — относительный универсальный код ресурса (URI) метода gRPC, например /greet.Greeter/SayHellos.</span><span class="sxs-lookup"><span data-stu-id="8da7e-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="8da7e-154">Пример выходных данных ведения журнала</span><span class="sxs-lookup"><span data-stu-id="8da7e-154">Sample logging output</span></span>

<span data-ttu-id="8da7e-155">Вот пример выходных данных консоли для клиента gRPC на уровне `Debug`:</span><span class="sxs-lookup"><span data-stu-id="8da7e-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a><span data-ttu-id="8da7e-156">Трассировка</span><span class="sxs-lookup"><span data-stu-id="8da7e-156">Tracing</span></span>

<span data-ttu-id="8da7e-157">Службы gRPC и клиент gRPC предоставляют сведения о вызовах gRPC с помощью классов [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) (источник диагностических данных) и [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity) (действие).</span><span class="sxs-lookup"><span data-stu-id="8da7e-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="8da7e-158">.NET gRPC использует действие для представления вызова gRPC.</span><span class="sxs-lookup"><span data-stu-id="8da7e-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="8da7e-159">События трассировки записываются в источник диагностических данных в начале и конце действия вызова gRPC.</span><span class="sxs-lookup"><span data-stu-id="8da7e-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="8da7e-160">При трассировке не собираются сведения о том, когда в течение времени существования вызовов потоковой передачи gRPC отправляются сообщения.</span><span class="sxs-lookup"><span data-stu-id="8da7e-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="8da7e-161">Трассировка службы gRPC</span><span class="sxs-lookup"><span data-stu-id="8da7e-161">gRPC service tracing</span></span>

<span data-ttu-id="8da7e-162">Службы gRPC размещаются на платформе ASP.NET Core, которая сообщает события, связанные с входящими HTTP-запросами.</span><span class="sxs-lookup"><span data-stu-id="8da7e-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="8da7e-163">К существующим данным диагностики HTTP-запросов, которые предоставляет платформа ASP.NET Core, добавляются метаданные, связанные с gRPC.</span><span class="sxs-lookup"><span data-stu-id="8da7e-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="8da7e-164">Имя источника диагностических данных — `Microsoft.AspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="8da7e-165">Имя действия — `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="8da7e-166">Имя метода gRPC в вызове gRPC добавляется в качестве тега с именем `grpc.method`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="8da7e-167">Код состояния вызова gRPC после его завершения добавляется в качестве тега с именем `grpc.status_code`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="8da7e-168">Трассировка клиента gRPC</span><span class="sxs-lookup"><span data-stu-id="8da7e-168">gRPC client tracing</span></span>

<span data-ttu-id="8da7e-169">Клиент .NET gRPC использует `HttpClient` для выполнения вызовов gRPC.</span><span class="sxs-lookup"><span data-stu-id="8da7e-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="8da7e-170">Хотя `HttpClient` записывает события диагностики, клиент .NET gRPC предоставляет пользовательский источник диагностических данных, действия и события для сбора полных сведений о вызове gRPC.</span><span class="sxs-lookup"><span data-stu-id="8da7e-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="8da7e-171">Имя источника диагностических данных — `Grpc.Net.Client`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="8da7e-172">Имя действия — `Grpc.Net.Client.GrpcOut`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="8da7e-173">Имя метода gRPC в вызове gRPC добавляется в качестве тега с именем `grpc.method`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="8da7e-174">Код состояния вызова gRPC после его завершения добавляется в качестве тега с именем `grpc.status_code`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="8da7e-175">Сбор трассировки</span><span class="sxs-lookup"><span data-stu-id="8da7e-175">Collecting tracing</span></span>

<span data-ttu-id="8da7e-176">Самый простой способ использовать `DiagnosticSource` — настроить в приложении библиотеку телеметрии, например [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) или [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span><span class="sxs-lookup"><span data-stu-id="8da7e-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="8da7e-177">Библиотека будет обрабатывать сведения о вызовах gRPC вместе с другими данными телеметрии приложения.</span><span class="sxs-lookup"><span data-stu-id="8da7e-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="8da7e-178">Результаты трассировки можно просматривать в управляемой службе, например Application Insights, либо же можно использовать собственную распределенную систему трассировки.</span><span class="sxs-lookup"><span data-stu-id="8da7e-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="8da7e-179">OpenTelemetry поддерживает экспорт данных трассировки в [Jaeger](https://www.jaegertracing.io/) и [Zipkin](https://zipkin.io/).</span><span class="sxs-lookup"><span data-stu-id="8da7e-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="8da7e-180">`DiagnosticSource` может использовать события трассировки в коде с помощью `DiagnosticListener`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="8da7e-181">Сведения о прослушивании источника диагностических данных в коде см. в [руководстве пользователя DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="8da7e-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="8da7e-182">В настоящее время библиотеки телеметрии не собирают данные телеметрии `Grpc.Net.Client.GrpcOut`, относящиеся к gRPC.</span><span class="sxs-lookup"><span data-stu-id="8da7e-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="8da7e-183">Работа над реализацией сбора этих данных трассировки библиотеками телеметрии ведется.</span><span class="sxs-lookup"><span data-stu-id="8da7e-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="8da7e-184">Метрики</span><span class="sxs-lookup"><span data-stu-id="8da7e-184">Metrics</span></span>

<span data-ttu-id="8da7e-185">Метрики — это представление мер данных за интервалы времени, например количество запросов в секунду.</span><span class="sxs-lookup"><span data-stu-id="8da7e-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="8da7e-186">Метрики позволяют отслеживать общее состояние приложения.</span><span class="sxs-lookup"><span data-stu-id="8da7e-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="8da7e-187">Метрики .NET gRPC создаются с помощью `EventCounter`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="8da7e-188">Метрики службы gRPC</span><span class="sxs-lookup"><span data-stu-id="8da7e-188">gRPC service metrics</span></span>

<span data-ttu-id="8da7e-189">Метрики сервера gRPC формируются в источнике событий `Grpc.AspNetCore.Server`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="8da7e-190">Имя</span><span class="sxs-lookup"><span data-stu-id="8da7e-190">Name</span></span>                      | <span data-ttu-id="8da7e-191">Description</span><span class="sxs-lookup"><span data-stu-id="8da7e-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="8da7e-192">Total Calls</span><span class="sxs-lookup"><span data-stu-id="8da7e-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="8da7e-193">Текущие вызовы</span><span class="sxs-lookup"><span data-stu-id="8da7e-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="8da7e-194">Всего вызовов с ошибками</span><span class="sxs-lookup"><span data-stu-id="8da7e-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="8da7e-195">Всего вызовов с истекшим сроком</span><span class="sxs-lookup"><span data-stu-id="8da7e-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="8da7e-196">Всего отправленных сообщений</span><span class="sxs-lookup"><span data-stu-id="8da7e-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="8da7e-197">Всего полученных сообщений</span><span class="sxs-lookup"><span data-stu-id="8da7e-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="8da7e-198">Всего нереализованных вызовов</span><span class="sxs-lookup"><span data-stu-id="8da7e-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="8da7e-199">ASP.NET Core также предоставляет собственные метрики в источнике событий `Microsoft.AspNetCore.Hosting`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="8da7e-200">Метрики клиента gRPC</span><span class="sxs-lookup"><span data-stu-id="8da7e-200">gRPC client metrics</span></span>

<span data-ttu-id="8da7e-201">Метрики клиента gRPC формируются в источнике событий `Grpc.Net.Client`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="8da7e-202">Имя</span><span class="sxs-lookup"><span data-stu-id="8da7e-202">Name</span></span>                      | <span data-ttu-id="8da7e-203">Description</span><span class="sxs-lookup"><span data-stu-id="8da7e-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="8da7e-204">Total Calls</span><span class="sxs-lookup"><span data-stu-id="8da7e-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="8da7e-205">Текущие вызовы</span><span class="sxs-lookup"><span data-stu-id="8da7e-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="8da7e-206">Всего вызовов с ошибками</span><span class="sxs-lookup"><span data-stu-id="8da7e-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="8da7e-207">Всего вызовов с истекшим сроком</span><span class="sxs-lookup"><span data-stu-id="8da7e-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="8da7e-208">Всего отправленных сообщений</span><span class="sxs-lookup"><span data-stu-id="8da7e-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="8da7e-209">Всего полученных сообщений</span><span class="sxs-lookup"><span data-stu-id="8da7e-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="8da7e-210">Отслеживание метрик</span><span class="sxs-lookup"><span data-stu-id="8da7e-210">Observe metrics</span></span>

<span data-ttu-id="8da7e-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) — это средство мониторинга производительности и первого уровня анализа производительности.</span><span class="sxs-lookup"><span data-stu-id="8da7e-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="8da7e-212">Для отслеживания приложения .NET используйте имя поставщика `Grpc.AspNetCore.Server` или `Grpc.Net.Client`.</span><span class="sxs-lookup"><span data-stu-id="8da7e-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

<span data-ttu-id="8da7e-213">Еще один способ отслеживания метрик gRPC заключается в сборе данных счетчиков с помощью [пакета Microsoft.ApplicationInsights.EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) Application Insights.</span><span class="sxs-lookup"><span data-stu-id="8da7e-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="8da7e-214">После настройки Application Insights собирает показания стандартных счетчиков .NET во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="8da7e-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="8da7e-215">Показания счетчиков gRPC по умолчанию не собираются, но в Application Insights можно [включить дополнительные счетчики](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span><span class="sxs-lookup"><span data-stu-id="8da7e-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="8da7e-216">Счетчики gRPC, показания которых собираются Application Insights, указываются в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8da7e-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a><span data-ttu-id="8da7e-217">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="8da7e-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
