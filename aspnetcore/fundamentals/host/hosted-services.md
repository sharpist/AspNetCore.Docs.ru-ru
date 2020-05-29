---
<span data-ttu-id="a69e7-101">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a69e7-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a69e7-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a69e7-102">'Blazor'</span></span>
- <span data-ttu-id="a69e7-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a69e7-103">'Identity'</span></span>
- <span data-ttu-id="a69e7-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a69e7-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="a69e7-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a69e7-105">'Razor'</span></span>
- <span data-ttu-id="a69e7-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="a69e7-106">'SignalR' uid:</span></span> 

---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="a69e7-107">Фоновые задачи с размещенными службами в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a69e7-107">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="a69e7-108">Автор: [Джау Ли Хуань](https://github.com/huan086) (Jeow Li Huan)</span><span class="sxs-lookup"><span data-stu-id="a69e7-108">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a69e7-109">В ASP.NET Core фоновые задачи реализуются как *размещенные службы*.</span><span class="sxs-lookup"><span data-stu-id="a69e7-109">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="a69e7-110">Размещенная служба — это класс с логикой фоновой задачи, реализующий интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="a69e7-110">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a69e7-111">Этот раздел содержит три примера размещенных служб:</span><span class="sxs-lookup"><span data-stu-id="a69e7-111">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="a69e7-112">Фоновая задача, которая выполняется по таймеру.</span><span class="sxs-lookup"><span data-stu-id="a69e7-112">Background task that runs on a timer.</span></span>
* <span data-ttu-id="a69e7-113">Размещенная служба, которая активирует [службу с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="a69e7-113">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="a69e7-114">Служба с заданной областью может использовать [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a69e7-114">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="a69e7-115">Очередь фоновых задач, которые выполняются последовательно.</span><span class="sxs-lookup"><span data-stu-id="a69e7-115">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="a69e7-116">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a69e7-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="a69e7-117">Шаблон службы рабочей роли</span><span class="sxs-lookup"><span data-stu-id="a69e7-117">Worker Service template</span></span>

<span data-ttu-id="a69e7-118">Шаблон службы рабочей роли ASP.NET Core может служить отправной точкой для написания длительно выполняющихся приложений служб.</span><span class="sxs-lookup"><span data-stu-id="a69e7-118">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="a69e7-119">Приложение, созданное из шаблона рабочей службы, указывает рабочий пакет SDK в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="a69e7-119">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="a69e7-120">Чтобы использовать шаблон в качестве основы для приложения размещенных служб, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="a69e7-120">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="a69e7-121">Пакет</span><span class="sxs-lookup"><span data-stu-id="a69e7-121">Package</span></span>

<span data-ttu-id="a69e7-122">Приложение, основанное на шаблоне рабочей службы, использует пакет SDK для `Microsoft.NET.Sdk.Worker` и имеет явную ссылку на пакет [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="a69e7-122">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="a69e7-123">Например, ознакомьтесь с файлом проекта в примере приложения (*BackgroundTasksSample csproj*).</span><span class="sxs-lookup"><span data-stu-id="a69e7-123">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="a69e7-124">Для веб-приложений, использующих пакет SDK `Microsoft.NET.Sdk.Web`, ссылка на пакет [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) указывается неявным образом из общей платформы.</span><span class="sxs-lookup"><span data-stu-id="a69e7-124">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="a69e7-125">Явная ссылка на пакет в файле проекта приложения не требуется.</span><span class="sxs-lookup"><span data-stu-id="a69e7-125">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="a69e7-126">Интерфейс IHostedService</span><span class="sxs-lookup"><span data-stu-id="a69e7-126">IHostedService interface</span></span>

<span data-ttu-id="a69e7-127">Интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService> определяет два метода для объектов, которые управляются узлом:</span><span class="sxs-lookup"><span data-stu-id="a69e7-127">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="a69e7-128">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` содержит логику для запуска фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="a69e7-128">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="a69e7-129">*Первым* вызывается `StartAsync`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-129">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="a69e7-130">Настраивается конвейер обработки запросов приложения (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="a69e7-130">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="a69e7-131">Запускается сервер и активируется [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*).</span><span class="sxs-lookup"><span data-stu-id="a69e7-131">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="a69e7-132">Поведение по умолчанию можно изменить таким образом, чтобы `StartAsync` размещенной службы выполнялся после настройки конвейера приложения и вызова `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-132">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="a69e7-133">Чтобы изменить поведение по умолчанию, добавьте размещенную службу (`VideosWatcher` в следующем примере) после вызова `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-133">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="a69e7-134">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; запускается, когда происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="a69e7-134">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="a69e7-135">`StopAsync` содержит логику для завершения фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="a69e7-135">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="a69e7-136">Реализуйте <xref:System.IDisposable> и [методы завершения (деструкторы)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) для освобождения неуправляемых ресурсов.</span><span class="sxs-lookup"><span data-stu-id="a69e7-136">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="a69e7-137">Токен отмены использует заданное по умолчанию 5-секундное время ожидания, указывающее, что процесс завершения работы больше не должен быть нормальным.</span><span class="sxs-lookup"><span data-stu-id="a69e7-137">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="a69e7-138">При запросе отмены происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="a69e7-138">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="a69e7-139">должны быть прерваны все оставшиеся фоновые операции, выполняемые приложением;</span><span class="sxs-lookup"><span data-stu-id="a69e7-139">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="a69e7-140">должны быть незамедлительно возвращены все методы, вызываемые в `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-140">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="a69e7-141">Однако после запроса отмены выполнение задач не прекращается &mdash; вызывающий объект ожидает завершения всех задач.</span><span class="sxs-lookup"><span data-stu-id="a69e7-141">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="a69e7-142">Если приложение завершает работу неожиданно (например, при сбое процесса приложения), `StopAsync` может не вызываться.</span><span class="sxs-lookup"><span data-stu-id="a69e7-142">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="a69e7-143">Поэтому вызов методов или выполнение операций в `StopAsync` может быть невозможным.</span><span class="sxs-lookup"><span data-stu-id="a69e7-143">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="a69e7-144">Чтобы увеличить время ожидания завершения работы по умолчанию (пять секунд), установите следующие значения:</span><span class="sxs-lookup"><span data-stu-id="a69e7-144">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="a69e7-145"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> при использовании универсального узла.</span><span class="sxs-lookup"><span data-stu-id="a69e7-145"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="a69e7-146">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a69e7-146">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="a69e7-147">Параметр конфигурации узла для времени ожидания завершения работы при использовании веб-узла.</span><span class="sxs-lookup"><span data-stu-id="a69e7-147">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="a69e7-148">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a69e7-148">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="a69e7-149">Размещенная служба активируется при запуске приложения и нормально завершает работу при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="a69e7-149">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="a69e7-150">Если во время выполнения задачи в фоновом режиме возникает ошибка, необходимо вызвать `Dispose`, даже если `StopAsync` не вызывается.</span><span class="sxs-lookup"><span data-stu-id="a69e7-150">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="a69e7-151">Базовый класс BackgroundService</span><span class="sxs-lookup"><span data-stu-id="a69e7-151">BackgroundService base class</span></span>

<span data-ttu-id="a69e7-152"><xref:Microsoft.Extensions.Hosting.BackgroundService> — это базовый класс для реализации долго выполняющегося интерфейса <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="a69e7-152"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="a69e7-153">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) вызывается для запуска фоновой службы.</span><span class="sxs-lookup"><span data-stu-id="a69e7-153">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="a69e7-154">Реализация возвращает значение <xref:System.Threading.Tasks.Task>, представляющее все время существования фоновой службы.</span><span class="sxs-lookup"><span data-stu-id="a69e7-154">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="a69e7-155">Дальнейшие службы не запустятся до тех пор, пока [ExecuteAsync не станет асинхронной](https://github.com/dotnet/extensions/issues/2149), например, путем вызова `await`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-155">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="a69e7-156">Старайтесь не выполнять функцию в течение длительного времени, так как инициализация в `ExecuteAsync` будет заблокирована.</span><span class="sxs-lookup"><span data-stu-id="a69e7-156">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="a69e7-157">Блоки узлов в [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) ожидают завершения `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-157">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="a69e7-158">Токен отмены активируется при вызове [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*).</span><span class="sxs-lookup"><span data-stu-id="a69e7-158">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="a69e7-159">При выдаче токена отмены реализация `ExecuteAsync` должна быстро завершиться для корректного завершения работы службы.</span><span class="sxs-lookup"><span data-stu-id="a69e7-159">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="a69e7-160">В противном случае служба некорректно завершает работу при истечении времени ожидания завершения работы.</span><span class="sxs-lookup"><span data-stu-id="a69e7-160">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="a69e7-161">Дополнительные сведения см. в разделе об [интерфейсе IHostedService](#ihostedservice-interface).</span><span class="sxs-lookup"><span data-stu-id="a69e7-161">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="a69e7-162">Фоновые задачи с заданным временем</span><span class="sxs-lookup"><span data-stu-id="a69e7-162">Timed background tasks</span></span>

<span data-ttu-id="a69e7-163">Для фоновых задач с заданным временем используется класс [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="a69e7-163">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="a69e7-164">Таймер запускает метод `DoWork` задачи.</span><span class="sxs-lookup"><span data-stu-id="a69e7-164">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="a69e7-165">Таймер отключается методом `StopAsync` и удаляется при удалении контейнера службы методом `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-165">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="a69e7-166"><xref:System.Threading.Timer> не ждет завершения предыдущего метода `DoWork`, поэтому приведенный подход может подойти не для всех сценариев.</span><span class="sxs-lookup"><span data-stu-id="a69e7-166">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="a69e7-167">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) используется для увеличения значений счетчика выполнения в виде атомарной операции. Благодаря этому несколько потоков не будут обновлять `executionCount` одновременно.</span><span class="sxs-lookup"><span data-stu-id="a69e7-167">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="a69e7-168">Служба регистрируется в `IHostBuilder.ConfigureServices` (*Program.cs*) с использованием метода расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-168">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="a69e7-169">Использование службы с заданной областью в фоновой задаче</span><span class="sxs-lookup"><span data-stu-id="a69e7-169">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="a69e7-170">Чтобы использовать [службы с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes) в [BackgroundService](#backgroundservice-base-class), создайте область.</span><span class="sxs-lookup"><span data-stu-id="a69e7-170">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="a69e7-171">Для размещенной службы по умолчанию не создается область.</span><span class="sxs-lookup"><span data-stu-id="a69e7-171">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="a69e7-172">Служба фоновой задачи с заданной областью содержит логику фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="a69e7-172">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="a69e7-173">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="a69e7-173">In the following example:</span></span>

* <span data-ttu-id="a69e7-174">Служба является асинхронной.</span><span class="sxs-lookup"><span data-stu-id="a69e7-174">The service is asynchronous.</span></span> <span data-ttu-id="a69e7-175">Метод `DoWork` возвращает значение `Task`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-175">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="a69e7-176">В демонстрационных целях в методе `DoWork` ожидается задержка в десять секунд.</span><span class="sxs-lookup"><span data-stu-id="a69e7-176">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="a69e7-177">В службу вставляется <xref:Microsoft.Extensions.Logging.ILogger>.</span><span class="sxs-lookup"><span data-stu-id="a69e7-177">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="a69e7-178">Размещенная служба создает область для разрешения службы фоновой задачи с заданной областью, чтобы вызвать ее метод `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-178">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="a69e7-179">`DoWork` возвращает объект `Task`, ожидаемый в `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-179">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="a69e7-180">Службы регистрируются в `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="a69e7-180">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="a69e7-181">Размещенная служба регистрируется с использованием метода расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-181">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="a69e7-182">Фоновые задачи в очереди</span><span class="sxs-lookup"><span data-stu-id="a69e7-182">Queued background tasks</span></span>

<span data-ttu-id="a69e7-183">Очередь фоновых задач основывается на методе <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> в .NET 4.x ([предварительно запланировано встроить в ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="a69e7-183">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="a69e7-184">В следующем примере `QueueHostedService`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-184">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="a69e7-185">Метод `BackgroundProcessing` возвращает объект `Task`, ожидаемый в `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-185">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="a69e7-186">Фоновые задачи в очереди выводятся из очереди и выполняются в `BackgroundProcessing`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-186">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="a69e7-187">Рабочие элементы ожидают остановки службы через `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-187">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="a69e7-188">Служба `MonitorLoop` обрабатывает задачи постановки в очередь для размещенной службы при выборе на устройстве ввода ключа `w`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-188">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="a69e7-189">В службу `MonitorLoop` внедряется `IBackgroundTaskQueue`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-189">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="a69e7-190">`IBackgroundTaskQueue.QueueBackgroundWorkItem` вызывается для постановки рабочего элемента в очередь:</span><span class="sxs-lookup"><span data-stu-id="a69e7-190">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="a69e7-191">Рабочий элемент имитирует долго выполняющуюся фоновую задачу:</span><span class="sxs-lookup"><span data-stu-id="a69e7-191">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="a69e7-192">Выполняется три 5-секундных задержки (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="a69e7-192">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="a69e7-193">Оператор `try-catch` перехватывается <xref:System.OperationCanceledException>, если задача отменена.</span><span class="sxs-lookup"><span data-stu-id="a69e7-193">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="a69e7-194">Службы регистрируются в `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="a69e7-194">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="a69e7-195">Размещенная служба регистрируется с использованием метода расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-195">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="a69e7-196">`MonitorLoop` запущен в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-196">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a69e7-197">В ASP.NET Core фоновые задачи реализуются как *размещенные службы*.</span><span class="sxs-lookup"><span data-stu-id="a69e7-197">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="a69e7-198">Размещенная служба — это класс с логикой фоновой задачи, реализующий интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="a69e7-198">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a69e7-199">Этот раздел содержит три примера размещенных служб:</span><span class="sxs-lookup"><span data-stu-id="a69e7-199">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="a69e7-200">Фоновая задача, которая выполняется по таймеру.</span><span class="sxs-lookup"><span data-stu-id="a69e7-200">Background task that runs on a timer.</span></span>
* <span data-ttu-id="a69e7-201">Размещенная служба, которая активирует [службу с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="a69e7-201">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="a69e7-202">Служба с заданной областью может использовать [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a69e7-202">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="a69e7-203">Очередь фоновых задач, которые выполняются последовательно.</span><span class="sxs-lookup"><span data-stu-id="a69e7-203">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="a69e7-204">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a69e7-204">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="a69e7-205">Пакет</span><span class="sxs-lookup"><span data-stu-id="a69e7-205">Package</span></span>

<span data-ttu-id="a69e7-206">Добавьте ссылку на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) или добавьте ссылку на пакет в пакет [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="a69e7-206">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="a69e7-207">Интерфейс IHostedService</span><span class="sxs-lookup"><span data-stu-id="a69e7-207">IHostedService interface</span></span>

<span data-ttu-id="a69e7-208">Размещенные службы реализуют интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="a69e7-208">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a69e7-209">Этот интерфейс определяет два метода для объектов, которые управляются узлом:</span><span class="sxs-lookup"><span data-stu-id="a69e7-209">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="a69e7-210">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` содержит логику для запуска фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="a69e7-210">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="a69e7-211">При использовании [Web Host](xref:fundamentals/host/web-host)`StartAsync` вызывается после запуска сервера и активации [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*).</span><span class="sxs-lookup"><span data-stu-id="a69e7-211">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="a69e7-212">При использовании [универсального узла](xref:fundamentals/host/generic-host)`StartAsync` вызывается до активации `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-212">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="a69e7-213">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; запускается, когда происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="a69e7-213">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="a69e7-214">`StopAsync` содержит логику для завершения фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="a69e7-214">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="a69e7-215">Реализуйте <xref:System.IDisposable> и [методы завершения (деструкторы)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) для освобождения неуправляемых ресурсов.</span><span class="sxs-lookup"><span data-stu-id="a69e7-215">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="a69e7-216">Токен отмены использует заданное по умолчанию 5-секундное время ожидания, указывающее, что процесс завершения работы больше не должен быть нормальным.</span><span class="sxs-lookup"><span data-stu-id="a69e7-216">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="a69e7-217">При запросе отмены происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="a69e7-217">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="a69e7-218">должны быть прерваны все оставшиеся фоновые операции, выполняемые приложением;</span><span class="sxs-lookup"><span data-stu-id="a69e7-218">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="a69e7-219">должны быть незамедлительно возвращены все методы, вызываемые в `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-219">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="a69e7-220">Однако после запроса отмены выполнение задач не прекращается &mdash; вызывающий объект ожидает завершения всех задач.</span><span class="sxs-lookup"><span data-stu-id="a69e7-220">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="a69e7-221">Если приложение завершает работу неожиданно (например, при сбое процесса приложения), `StopAsync` может не вызываться.</span><span class="sxs-lookup"><span data-stu-id="a69e7-221">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="a69e7-222">Поэтому вызов методов или выполнение операций в `StopAsync` может быть невозможным.</span><span class="sxs-lookup"><span data-stu-id="a69e7-222">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="a69e7-223">Чтобы увеличить время ожидания завершения работы по умолчанию (пять секунд), установите следующие значения:</span><span class="sxs-lookup"><span data-stu-id="a69e7-223">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="a69e7-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> при использовании универсального узла.</span><span class="sxs-lookup"><span data-stu-id="a69e7-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="a69e7-225">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a69e7-225">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="a69e7-226">Параметр конфигурации узла для времени ожидания завершения работы при использовании веб-узла.</span><span class="sxs-lookup"><span data-stu-id="a69e7-226">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="a69e7-227">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a69e7-227">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="a69e7-228">Размещенная служба активируется при запуске приложения и нормально завершает работу при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="a69e7-228">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="a69e7-229">Если во время выполнения задачи в фоновом режиме возникает ошибка, необходимо вызвать `Dispose`, даже если `StopAsync` не вызывается.</span><span class="sxs-lookup"><span data-stu-id="a69e7-229">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="a69e7-230">Фоновые задачи с заданным временем</span><span class="sxs-lookup"><span data-stu-id="a69e7-230">Timed background tasks</span></span>

<span data-ttu-id="a69e7-231">Для фоновых задач с заданным временем используется класс [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="a69e7-231">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="a69e7-232">Таймер запускает метод `DoWork` задачи.</span><span class="sxs-lookup"><span data-stu-id="a69e7-232">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="a69e7-233">Таймер отключается методом `StopAsync` и удаляется при удалении контейнера службы методом `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-233">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="a69e7-234"><xref:System.Threading.Timer> не ждет завершения предыдущего метода `DoWork`, поэтому приведенный подход может подойти не для всех сценариев.</span><span class="sxs-lookup"><span data-stu-id="a69e7-234">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="a69e7-235">Служба зарегистрирована в `Startup.ConfigureServices` с методом расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-235">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="a69e7-236">Использование службы с заданной областью в фоновой задаче</span><span class="sxs-lookup"><span data-stu-id="a69e7-236">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="a69e7-237">Чтобы использовать [службы с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes) в `IHostedService`, создайте область.</span><span class="sxs-lookup"><span data-stu-id="a69e7-237">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="a69e7-238">Для размещенной службы по умолчанию не создается область.</span><span class="sxs-lookup"><span data-stu-id="a69e7-238">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="a69e7-239">Служба фоновой задачи с заданной областью содержит логику фоновой задачи.</span><span class="sxs-lookup"><span data-stu-id="a69e7-239">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="a69e7-240">В следующем примере в службу вставляется <xref:Microsoft.Extensions.Logging.ILogger>:</span><span class="sxs-lookup"><span data-stu-id="a69e7-240">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="a69e7-241">Размещенная служба создает область для разрешения службы фоновой задачи с заданной областью, чтобы вызвать ее метод `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-241">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="a69e7-242">Службы регистрируются в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-242">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a69e7-243">Реализация `IHostedService` зарегистрирована с методом расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-243">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="a69e7-244">Фоновые задачи в очереди</span><span class="sxs-lookup"><span data-stu-id="a69e7-244">Queued background tasks</span></span>

<span data-ttu-id="a69e7-245">Очередь фоновых задач основывается на методе <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> в .NET Framework 4.x ([предварительно запланировано встроить в ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="a69e7-245">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="a69e7-246">В `QueueHostedService` фоновые задачи в очереди из очереди выводятся из очереди и выполняются в качестве [BackgroundService](#backgroundservice-base-class) — базового класса для реализации длительного выполнения `IHostedService`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-246">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="a69e7-247">Службы регистрируются в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-247">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a69e7-248">Реализация `IHostedService` зарегистрирована с методом расширения `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="a69e7-248">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="a69e7-249">В классе модели страницы индексов:</span><span class="sxs-lookup"><span data-stu-id="a69e7-249">In the Index page model class:</span></span>

* <span data-ttu-id="a69e7-250">`IBackgroundTaskQueue` вставляется в конструктор и присваивается `Queue`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-250">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="a69e7-251"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> вставляется и присваивается `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-251">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="a69e7-252">Фабрика используется для создания экземпляров <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, который используется для создания служб с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="a69e7-252">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="a69e7-253">Для использования `AppDbContext` приложения создается область ([служба с заданной областью](xref:fundamentals/dependency-injection#service-lifetimes)) для записи данных из базы данных в `IBackgroundTaskQueue` (отдельная служба).</span><span class="sxs-lookup"><span data-stu-id="a69e7-253">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="a69e7-254">Если нажать кнопку **Добавить задачу** на странице индексов, выполняется метод `OnPostAddTask`.</span><span class="sxs-lookup"><span data-stu-id="a69e7-254">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="a69e7-255">`QueueBackgroundWorkItem` вызывается для постановки рабочего элемента в очередь:</span><span class="sxs-lookup"><span data-stu-id="a69e7-255">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a69e7-256">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a69e7-256">Additional resources</span></span>

* [<span data-ttu-id="a69e7-257">Реализация фоновых задач в микрослужбах с помощью IHostedService и класса BackgroundService</span><span class="sxs-lookup"><span data-stu-id="a69e7-257">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="a69e7-258">Выполнение фоновых задач с помощью веб-заданий в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="a69e7-258">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
