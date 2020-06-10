---
title: 'title: Фоновые задачи с размещенными службами в ASP.NET Core author: rick-anderson description: Узнайте, как реализовать фоновые задачи с размещенными службами в ASP.NET Core.'
author: rick-anderson
description: "monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date: 10.02.2020 no-loc:"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 47d0bdda7249232af22ec1c97e7baa710310caed
ms.sourcegitcommit: cb6e3e12641375ea9ad02002388f78ec0989d88e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84253686"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="df9d6-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="df9d6-103">'Blazor'</span></span>

<span data-ttu-id="df9d6-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="df9d6-104">'Identity'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="df9d6-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="df9d6-105">'Let's Encrypt'</span></span> <span data-ttu-id="df9d6-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="df9d6-106">'Razor'</span></span> <span data-ttu-id="df9d6-107">'SignalR' uid: fundamentals/host/hosted-services</span><span class="sxs-lookup"><span data-stu-id="df9d6-107">'SignalR' uid: fundamentals/host/hosted-services</span></span>

* <span data-ttu-id="df9d6-108">Фоновые задачи с размещенными службами в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df9d6-108">Background tasks with hosted services in ASP.NET Core</span></span>
* <span data-ttu-id="df9d6-109">Автор: [Джау Ли Хуань](https://github.com/huan086) (Jeow Li Huan)</span><span class="sxs-lookup"><span data-stu-id="df9d6-109">By [Jeow Li Huan](https://github.com/huan086)</span></span> <span data-ttu-id="df9d6-110">В ASP.NET Core фоновые задачи реализуются как *размещенные службы*.</span><span class="sxs-lookup"><span data-stu-id="df9d6-110">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span>
* <span data-ttu-id="df9d6-111">Размещенная служба — это класс с логикой фоновой задачи, реализующий интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="df9d6-111">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span>

<span data-ttu-id="df9d6-112">Этот раздел содержит три примера размещенных служб:</span><span class="sxs-lookup"><span data-stu-id="df9d6-112">This topic provides three hosted service examples:</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="df9d6-113">Фоновая задача, которая выполняется по таймеру.</span><span class="sxs-lookup"><span data-stu-id="df9d6-113">Background task that runs on a timer.</span></span>

<span data-ttu-id="df9d6-114">Размещенная служба, которая активирует [службу с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="df9d6-114">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="df9d6-115">Служба с заданной областью может использовать [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="df9d6-115">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="df9d6-116">Очередь фоновых задач, которые выполняются последовательно.</span><span class="sxs-lookup"><span data-stu-id="df9d6-116">Queued background tasks that run sequentially.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="df9d6-117">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="df9d6-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="df9d6-118">Шаблон службы рабочей роли</span><span class="sxs-lookup"><span data-stu-id="df9d6-118">Worker Service template</span></span> <span data-ttu-id="df9d6-119">Шаблон службы рабочей роли ASP.NET Core может служить отправной точкой для написания длительно выполняющихся приложений служб.</span><span class="sxs-lookup"><span data-stu-id="df9d6-119">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span>

<span data-ttu-id="df9d6-120">Приложение, созданное из шаблона рабочей службы, указывает рабочий пакет SDK в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="df9d6-120">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span> <span data-ttu-id="df9d6-121">Чтобы использовать шаблон в качестве основы для приложения размещенных служб, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="df9d6-121">To use the template as a basis for a hosted services app:</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="df9d6-122">Пакет</span><span class="sxs-lookup"><span data-stu-id="df9d6-122">Package</span></span>

<span data-ttu-id="df9d6-123">Приложение, основанное на шаблоне рабочей службы, использует пакет SDK для `Microsoft.NET.Sdk.Worker` и имеет явную ссылку на пакет [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="df9d6-123">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

* <span data-ttu-id="df9d6-124">Например, ознакомьтесь с файлом проекта в примере приложения (*BackgroundTasksSample csproj*).</span><span class="sxs-lookup"><span data-stu-id="df9d6-124">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span> <span data-ttu-id="df9d6-125">Для веб-приложений, использующих пакет SDK `Microsoft.NET.Sdk.Web`, ссылка на пакет [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) указывается неявным образом из общей платформы.</span><span class="sxs-lookup"><span data-stu-id="df9d6-125">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span>

  * <span data-ttu-id="df9d6-126">Явная ссылка на пакет в файле проекта приложения не требуется.</span><span class="sxs-lookup"><span data-stu-id="df9d6-126">An explicit package reference in the app's project file isn't required.</span></span>
  * <span data-ttu-id="df9d6-127">Интерфейс IHostedService</span><span class="sxs-lookup"><span data-stu-id="df9d6-127">IHostedService interface</span></span>

  <span data-ttu-id="df9d6-128">Интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService> определяет два метода для объектов, которые управляются узлом:</span><span class="sxs-lookup"><span data-stu-id="df9d6-128">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span> <span data-ttu-id="df9d6-129">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` содержит логику для запуска фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="df9d6-129">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span>

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

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
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* <span data-ttu-id="df9d6-130">*Первым* вызывается `StartAsync`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-130">`StartAsync` is called *before*:</span></span> <span data-ttu-id="df9d6-131">Настраивается конвейер обработки запросов приложения (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="df9d6-131">The app's request processing pipeline is configured (`Startup.Configure`).</span></span> <span data-ttu-id="df9d6-132">Запускается сервер и активируется [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*).</span><span class="sxs-lookup"><span data-stu-id="df9d6-132">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="df9d6-133">Поведение по умолчанию можно изменить таким образом, чтобы `StartAsync` размещенной службы выполнялся после настройки конвейера приложения и вызова `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-133">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="df9d6-134">Чтобы изменить поведение по умолчанию, добавьте размещенную службу (`VideosWatcher` в следующем примере) после вызова `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-134">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

  * <span data-ttu-id="df9d6-135">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): активируется, когда происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="df9d6-135">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span>
  * <span data-ttu-id="df9d6-136">`StopAsync` содержит логику для завершения фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="df9d6-136">`StopAsync` contains the logic to end the background task.</span></span>

  <span data-ttu-id="df9d6-137">Реализуйте <xref:System.IDisposable> и [методы завершения (деструкторы)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) для освобождения неуправляемых ресурсов.</span><span class="sxs-lookup"><span data-stu-id="df9d6-137">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="df9d6-138">Токен отмены использует заданное по умолчанию 5-секундное время ожидания, указывающее, что процесс завершения работы больше не должен быть нормальным.</span><span class="sxs-lookup"><span data-stu-id="df9d6-138">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="df9d6-139">При запросе отмены происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="df9d6-139">When cancellation is requested on the token:</span></span>

  <span data-ttu-id="df9d6-140">должны быть прерваны все оставшиеся фоновые операции, выполняемые приложением;</span><span class="sxs-lookup"><span data-stu-id="df9d6-140">Any remaining background operations that the app is performing should be aborted.</span></span>

  * <span data-ttu-id="df9d6-141">должны быть незамедлительно возвращены все методы, вызываемые в `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-141">Any methods called in `StopAsync` should return promptly.</span></span> <span data-ttu-id="df9d6-142">Однако после запроса отмены выполнение задач не прекращается &mdash; вызывающий объект ожидает завершения всех задач.</span><span class="sxs-lookup"><span data-stu-id="df9d6-142">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>
  * <span data-ttu-id="df9d6-143">Если приложение завершает работу неожиданно (например, при сбое процесса приложения), `StopAsync` может не вызываться.</span><span class="sxs-lookup"><span data-stu-id="df9d6-143">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="df9d6-144">Поэтому вызов методов или выполнение операций в `StopAsync` может быть невозможным.</span><span class="sxs-lookup"><span data-stu-id="df9d6-144">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

<span data-ttu-id="df9d6-145">Чтобы увеличить время ожидания завершения работы по умолчанию (пять секунд), установите следующие значения:</span><span class="sxs-lookup"><span data-stu-id="df9d6-145">To extend the default five second shutdown timeout, set:</span></span> <span data-ttu-id="df9d6-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> при использовании универсального узла.</span><span class="sxs-lookup"><span data-stu-id="df9d6-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="df9d6-147">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="df9d6-147">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>

<span data-ttu-id="df9d6-148">Параметр конфигурации узла для времени ожидания завершения работы при использовании веб-узла.</span><span class="sxs-lookup"><span data-stu-id="df9d6-148">Shutdown timeout host configuration setting when using Web Host.</span></span>

<span data-ttu-id="df9d6-149">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="df9d6-149">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span> <span data-ttu-id="df9d6-150">Размещенная служба активируется при запуске приложения и нормально завершает работу при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="df9d6-150">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="df9d6-151">Если во время выполнения задачи в фоновом режиме возникает ошибка, необходимо вызвать `Dispose`, даже если `StopAsync` не вызывается.</span><span class="sxs-lookup"><span data-stu-id="df9d6-151">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span> <span data-ttu-id="df9d6-152">Базовый класс BackgroundService</span><span class="sxs-lookup"><span data-stu-id="df9d6-152">BackgroundService base class</span></span> <span data-ttu-id="df9d6-153"><xref:Microsoft.Extensions.Hosting.BackgroundService> — это базовый класс для реализации долго выполняющегося интерфейса <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="df9d6-153"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="df9d6-154">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) вызывается для запуска фоновой службы.</span><span class="sxs-lookup"><span data-stu-id="df9d6-154">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="df9d6-155">Реализация возвращает значение <xref:System.Threading.Tasks.Task>, представляющее все время существования фоновой службы.</span><span class="sxs-lookup"><span data-stu-id="df9d6-155">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="df9d6-156">Дальнейшие службы не запустятся до тех пор, пока [ExecuteAsync не станет асинхронной](https://github.com/dotnet/extensions/issues/2149), например, путем вызова `await`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-156">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="df9d6-157">Старайтесь не выполнять функцию в течение длительного времени, так как инициализация в `ExecuteAsync` будет заблокирована.</span><span class="sxs-lookup"><span data-stu-id="df9d6-157">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="df9d6-158">Блоки узлов в [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) ожидают завершения `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-158">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="df9d6-159">Токен отмены активируется при вызове [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*).</span><span class="sxs-lookup"><span data-stu-id="df9d6-159">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="df9d6-160">При выдаче токена отмены реализация `ExecuteAsync` должна быстро завершиться для корректного завершения работы службы.</span><span class="sxs-lookup"><span data-stu-id="df9d6-160">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="df9d6-161">В противном случае служба некорректно завершает работу при истечении времени ожидания завершения работы.</span><span class="sxs-lookup"><span data-stu-id="df9d6-161">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="df9d6-162">Дополнительные сведения см. в разделе об [интерфейсе IHostedService](#ihostedservice-interface).</span><span class="sxs-lookup"><span data-stu-id="df9d6-162">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span> <span data-ttu-id="df9d6-163">Фоновые задачи с заданным временем</span><span class="sxs-lookup"><span data-stu-id="df9d6-163">Timed background tasks</span></span>

<span data-ttu-id="df9d6-164">Для фоновых задач с заданным временем используется класс [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="df9d6-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="df9d6-165">Таймер запускает метод `DoWork` задачи.</span><span class="sxs-lookup"><span data-stu-id="df9d6-165">The timer triggers the task's `DoWork` method.</span></span>

<span data-ttu-id="df9d6-166">Таймер отключается методом `StopAsync` и удаляется при удалении контейнера службы методом `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span> <span data-ttu-id="df9d6-167"><xref:System.Threading.Timer> не ждет завершения предыдущего метода `DoWork`, поэтому приведенный подход может подойти не для всех сценариев.</span><span class="sxs-lookup"><span data-stu-id="df9d6-167">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="df9d6-168">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) используется для увеличения значений счетчика выполнения в виде атомарной операции. Благодаря этому несколько потоков не будут обновлять `executionCount` одновременно.</span><span class="sxs-lookup"><span data-stu-id="df9d6-168">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span> <span data-ttu-id="df9d6-169">Служба регистрируется в `IHostBuilder.ConfigureServices` (*Program.cs*) с использованием метода расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-169">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

* <span data-ttu-id="df9d6-170">Использование службы с заданной областью в фоновой задаче</span><span class="sxs-lookup"><span data-stu-id="df9d6-170">Consuming a scoped service in a background task</span></span> <span data-ttu-id="df9d6-171">Чтобы использовать [службы с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes) в [BackgroundService](#backgroundservice-base-class), создайте область.</span><span class="sxs-lookup"><span data-stu-id="df9d6-171">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="df9d6-172">Для размещенной службы по умолчанию не создается область.</span><span class="sxs-lookup"><span data-stu-id="df9d6-172">No scope is created for a hosted service by default.</span></span>
* <span data-ttu-id="df9d6-173">Служба фоновой задачи с заданной областью содержит логику фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="df9d6-173">The scoped background task service contains the background task's logic.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="df9d6-174">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="df9d6-174">In the following example:</span></span> <span data-ttu-id="df9d6-175">Служба является асинхронной.</span><span class="sxs-lookup"><span data-stu-id="df9d6-175">The service is asynchronous.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="df9d6-176">Метод `DoWork` возвращает значение `Task`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-176">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="df9d6-177">В демонстрационных целях в методе `DoWork` ожидается задержка в десять секунд.</span><span class="sxs-lookup"><span data-stu-id="df9d6-177">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="df9d6-178">В службу вставляется <xref:Microsoft.Extensions.Logging.ILogger>.</span><span class="sxs-lookup"><span data-stu-id="df9d6-178">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

<span data-ttu-id="df9d6-179">Размещенная служба создает область для разрешения службы фоновой задачи с заданной областью, чтобы вызвать ее метод `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-179">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="df9d6-180">`DoWork` возвращает объект `Task`, ожидаемый в `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-180">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

* <span data-ttu-id="df9d6-181">Службы регистрируются в `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="df9d6-181">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span>
* <span data-ttu-id="df9d6-182">Размещенная служба регистрируется с использованием метода расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-182">The hosted service is registered with the `AddHostedService` extension method:</span></span>
* <span data-ttu-id="df9d6-183">Фоновые задачи в очереди</span><span class="sxs-lookup"><span data-stu-id="df9d6-183">Queued background tasks</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="df9d6-184">Очередь фоновых задач основана на <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> .NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="df9d6-184">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span></span>

* <span data-ttu-id="df9d6-185">В следующем примере `QueueHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-185">In the following `QueueHostedService` example:</span></span>
* <span data-ttu-id="df9d6-186">Метод `BackgroundProcessing` возвращает объект `Task`, ожидаемый в `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-186">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="df9d6-187">Фоновые задачи в очереди выводятся из очереди и выполняются в `BackgroundProcessing`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-187">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
  * <span data-ttu-id="df9d6-188">Рабочие элементы ожидают остановки службы через `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-188">Work items are awaited before the service stops in `StopAsync`.</span></span>
  * <span data-ttu-id="df9d6-189">Служба `MonitorLoop` обрабатывает задачи постановки в очередь для размещенной службы при выборе на устройстве ввода ключа `w`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-189">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="df9d6-190">В службу `MonitorLoop` внедряется `IBackgroundTaskQueue`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-190">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span> <span data-ttu-id="df9d6-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem` вызывается для постановки рабочего элемента в очередь:</span><span class="sxs-lookup"><span data-stu-id="df9d6-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="df9d6-192">Рабочий элемент имитирует долго выполняющуюся фоновую задачу:</span><span class="sxs-lookup"><span data-stu-id="df9d6-192">The work item simulates a long-running background task:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="df9d6-193">Выполняется три 5-секундных задержки (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="df9d6-193">Three 5-second delays are executed (`Task.Delay`).</span></span> <span data-ttu-id="df9d6-194">Оператор `try-catch` перехватывается <xref:System.OperationCanceledException>, если задача отменена.</span><span class="sxs-lookup"><span data-stu-id="df9d6-194">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span> <span data-ttu-id="df9d6-195">Службы регистрируются в `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="df9d6-195">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span>

* <span data-ttu-id="df9d6-196">Размещенная служба регистрируется с использованием метода расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-196">The hosted service is registered with the `AddHostedService` extension method:</span></span>
* <span data-ttu-id="df9d6-197">`MonitorLoop` запущен в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-197">`MonitorLoop` is started in `Program.Main`:</span></span> <span data-ttu-id="df9d6-198">В ASP.NET Core фоновые задачи реализуются как *размещенные службы*.</span><span class="sxs-lookup"><span data-stu-id="df9d6-198">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span>
* <span data-ttu-id="df9d6-199">Размещенная служба — это класс с логикой фоновой задачи, реализующий интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="df9d6-199">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span>

<span data-ttu-id="df9d6-200">Этот раздел содержит три примера размещенных служб:</span><span class="sxs-lookup"><span data-stu-id="df9d6-200">This topic provides three hosted service examples:</span></span>

## <a name="package"></a><span data-ttu-id="df9d6-201">Фоновая задача, которая выполняется по таймеру.</span><span class="sxs-lookup"><span data-stu-id="df9d6-201">Background task that runs on a timer.</span></span>

<span data-ttu-id="df9d6-202">Размещенная служба, которая активирует [службу с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="df9d6-202">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="df9d6-203">Служба с заданной областью может использовать [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="df9d6-203">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>

<span data-ttu-id="df9d6-204">Очередь фоновых задач, которые выполняются последовательно.</span><span class="sxs-lookup"><span data-stu-id="df9d6-204">Queued background tasks that run sequentially.</span></span> <span data-ttu-id="df9d6-205">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="df9d6-205">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

* <span data-ttu-id="df9d6-206">Пакет</span><span class="sxs-lookup"><span data-stu-id="df9d6-206">Package</span></span> <span data-ttu-id="df9d6-207">Добавьте ссылку на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) или добавьте ссылку на пакет в пакет [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="df9d6-207">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="df9d6-208">Интерфейс IHostedService</span><span class="sxs-lookup"><span data-stu-id="df9d6-208">IHostedService interface</span></span>

* <span data-ttu-id="df9d6-209">Размещенные службы реализуют интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="df9d6-209">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="df9d6-210">Этот интерфейс определяет два метода для объектов, которые управляются узлом:</span><span class="sxs-lookup"><span data-stu-id="df9d6-210">The interface defines two methods for objects that are managed by the host:</span></span> <span data-ttu-id="df9d6-211">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` содержит логику для запуска фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="df9d6-211">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span>

  <span data-ttu-id="df9d6-212">При использовании [Web Host](xref:fundamentals/host/web-host)`StartAsync` вызывается после запуска сервера и активации [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*).</span><span class="sxs-lookup"><span data-stu-id="df9d6-212">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="df9d6-213">При использовании [универсального узла](xref:fundamentals/host/generic-host)`StartAsync` вызывается до активации `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-213">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

  * <span data-ttu-id="df9d6-214">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): активируется, когда происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="df9d6-214">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span>
  * <span data-ttu-id="df9d6-215">`StopAsync` содержит логику для завершения фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="df9d6-215">`StopAsync` contains the logic to end the background task.</span></span>

  <span data-ttu-id="df9d6-216">Реализуйте <xref:System.IDisposable> и [методы завершения (деструкторы)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) для освобождения неуправляемых ресурсов.</span><span class="sxs-lookup"><span data-stu-id="df9d6-216">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="df9d6-217">Токен отмены использует заданное по умолчанию 5-секундное время ожидания, указывающее, что процесс завершения работы больше не должен быть нормальным.</span><span class="sxs-lookup"><span data-stu-id="df9d6-217">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="df9d6-218">При запросе отмены происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="df9d6-218">When cancellation is requested on the token:</span></span>

  <span data-ttu-id="df9d6-219">должны быть прерваны все оставшиеся фоновые операции, выполняемые приложением;</span><span class="sxs-lookup"><span data-stu-id="df9d6-219">Any remaining background operations that the app is performing should be aborted.</span></span>

  * <span data-ttu-id="df9d6-220">должны быть незамедлительно возвращены все методы, вызываемые в `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-220">Any methods called in `StopAsync` should return promptly.</span></span> <span data-ttu-id="df9d6-221">Однако после запроса отмены выполнение задач не прекращается &mdash; вызывающий объект ожидает завершения всех задач.</span><span class="sxs-lookup"><span data-stu-id="df9d6-221">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>
  * <span data-ttu-id="df9d6-222">Если приложение завершает работу неожиданно (например, при сбое процесса приложения), `StopAsync` может не вызываться.</span><span class="sxs-lookup"><span data-stu-id="df9d6-222">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="df9d6-223">Поэтому вызов методов или выполнение операций в `StopAsync` может быть невозможным.</span><span class="sxs-lookup"><span data-stu-id="df9d6-223">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

<span data-ttu-id="df9d6-224">Чтобы увеличить время ожидания завершения работы по умолчанию (пять секунд), установите следующие значения:</span><span class="sxs-lookup"><span data-stu-id="df9d6-224">To extend the default five second shutdown timeout, set:</span></span> <span data-ttu-id="df9d6-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> при использовании универсального узла.</span><span class="sxs-lookup"><span data-stu-id="df9d6-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="df9d6-226">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="df9d6-226">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>

<span data-ttu-id="df9d6-227">Параметр конфигурации узла для времени ожидания завершения работы при использовании веб-узла.</span><span class="sxs-lookup"><span data-stu-id="df9d6-227">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="df9d6-228">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="df9d6-228">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span> <span data-ttu-id="df9d6-229">Размещенная служба активируется при запуске приложения и нормально завершает работу при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="df9d6-229">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="df9d6-230">Если во время выполнения задачи в фоновом режиме возникает ошибка, необходимо вызвать `Dispose`, даже если `StopAsync` не вызывается.</span><span class="sxs-lookup"><span data-stu-id="df9d6-230">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

<span data-ttu-id="df9d6-231">Фоновые задачи с заданным временем</span><span class="sxs-lookup"><span data-stu-id="df9d6-231">Timed background tasks</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="df9d6-232">Для фоновых задач с заданным временем используется класс [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="df9d6-232">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span>

<span data-ttu-id="df9d6-233">Таймер запускает метод `DoWork` задачи.</span><span class="sxs-lookup"><span data-stu-id="df9d6-233">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="df9d6-234">Таймер отключается методом `StopAsync` и удаляется при удалении контейнера службы методом `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-234">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

<span data-ttu-id="df9d6-235"><xref:System.Threading.Timer> не ждет завершения предыдущего метода `DoWork`, поэтому приведенный подход может подойти не для всех сценариев.</span><span class="sxs-lookup"><span data-stu-id="df9d6-235">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="df9d6-236">Служба зарегистрирована в `Startup.ConfigureServices` с методом расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-236">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="df9d6-237">Использование службы с заданной областью в фоновой задаче</span><span class="sxs-lookup"><span data-stu-id="df9d6-237">Consuming a scoped service in a background task</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="df9d6-238">Чтобы использовать [службы с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes) в `IHostedService`, создайте область.</span><span class="sxs-lookup"><span data-stu-id="df9d6-238">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="df9d6-239">Для размещенной службы по умолчанию не создается область.</span><span class="sxs-lookup"><span data-stu-id="df9d6-239">No scope is created for a hosted service by default.</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="df9d6-240">Служба фоновой задачи с заданной областью содержит логику фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="df9d6-240">The scoped background task service contains the background task's logic.</span></span>

<span data-ttu-id="df9d6-241">В следующем примере в службу вставляется <xref:Microsoft.Extensions.Logging.ILogger>:</span><span class="sxs-lookup"><span data-stu-id="df9d6-241">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="df9d6-242">Размещенная служба создает область для разрешения службы фоновой задачи с заданной областью, чтобы вызвать ее метод `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-242">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="df9d6-243">Службы регистрируются в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="df9d6-244">Реализация `IHostedService` зарегистрирована с методом расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="df9d6-245">Фоновые задачи в очереди</span><span class="sxs-lookup"><span data-stu-id="df9d6-245">Queued background tasks</span></span>

* <span data-ttu-id="df9d6-246">Очередь фоновых задач основывается на методе <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> в .NET Framework 4.x ([предварительно запланировано встроить в ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="df9d6-246">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>
* <span data-ttu-id="df9d6-247">В `QueueHostedService` фоновые задачи в очереди из очереди выводятся из очереди и выполняются в качестве [BackgroundService](#backgroundservice-base-class) — базового класса для реализации длительного выполнения `IHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-247">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span> <span data-ttu-id="df9d6-248">Службы регистрируются в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-248">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="df9d6-249">Реализация `IHostedService` зарегистрирована с методом расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df9d6-249">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="df9d6-250">В классе модели страницы индексов:</span><span class="sxs-lookup"><span data-stu-id="df9d6-250">In the Index page model class:</span></span> <span data-ttu-id="df9d6-251">`IBackgroundTaskQueue` вставляется в конструктор и присваивается `Queue`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-251">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="df9d6-252"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> вставляется и присваивается `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="df9d6-252">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span>

* <span data-ttu-id="df9d6-253">Фабрика используется для создания экземпляров <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, который используется для создания служб с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="df9d6-253">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span>
* <span data-ttu-id="df9d6-254">Для использования `AppDbContext` приложения создается область ([служба с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes)) для записи данных из базы данных в `IBackgroundTaskQueue` (отдельная служба).</span><span class="sxs-lookup"><span data-stu-id="df9d6-254">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>
* <xref:System.Threading.Timer>
