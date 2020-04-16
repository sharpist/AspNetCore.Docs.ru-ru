---
title: Управление памятью и шаблоны в ASP.NET Core
author: rick-anderson
description: Узнайте, как управляется память в ASP.NET Core и как работает сборщик мусора (GC).
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440952"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="98836-103">Управление памятью и сбор мусора (GC) в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98836-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="98836-104">[Себастьен Рос](https://github.com/sebastienros) и [Рик Андерсон](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="98836-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="98836-105">Управление памятью является сложным, даже в управляемой инфраструктуре, как .NET.</span><span class="sxs-lookup"><span data-stu-id="98836-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="98836-106">Анализ и понимание проблем памяти может быть сложной задачей.</span><span class="sxs-lookup"><span data-stu-id="98836-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="98836-107">В этой статье:</span><span class="sxs-lookup"><span data-stu-id="98836-107">This article:</span></span>

* <span data-ttu-id="98836-108">Был мотивирован много *утечки памяти* и *GC не рабочие* вопросы.</span><span class="sxs-lookup"><span data-stu-id="98836-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="98836-109">Большинство из этих проблем были вызваны непониманием того, как работает потребление памяти в .NET Core, или не пониманием того, как оно измеряется.</span><span class="sxs-lookup"><span data-stu-id="98836-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="98836-110">Демонстрирует проблематичное использование памяти и предлагает альтернативные подходы.</span><span class="sxs-lookup"><span data-stu-id="98836-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="98836-111">Как работает сбор мусора (GC) в ядро .NET</span><span class="sxs-lookup"><span data-stu-id="98836-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="98836-112">GC выделяет сегменты кучи, где каждый сегмент является смежным диапазоном памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="98836-113">Объекты, помещенные в кучу, классифицируются в одно из 3 поколений: 0, 1 или 2.</span><span class="sxs-lookup"><span data-stu-id="98836-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="98836-114">Поколение определяет частоту, на которую пытается GC выпустить память на управляемых объектах, на которые приложение больше не ссылается.</span><span class="sxs-lookup"><span data-stu-id="98836-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="98836-115">Более низкие пронумерованные поколения GC'd часее.</span><span class="sxs-lookup"><span data-stu-id="98836-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="98836-116">Объекты перемещаются из поколения в поколение в зависимости от их продолжительности жизни.</span><span class="sxs-lookup"><span data-stu-id="98836-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="98836-117">Поскольку объекты живут дольше, они перемещаются в более высокое поколение.</span><span class="sxs-lookup"><span data-stu-id="98836-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="98836-118">Как упоминалось ранее, высшие поколения GC'd реже.</span><span class="sxs-lookup"><span data-stu-id="98836-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="98836-119">Краткосрочные жили объекты всегда остаются в поколении 0.</span><span class="sxs-lookup"><span data-stu-id="98836-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="98836-120">Например, объекты, на которые ссылаются в течение срока службы веб-запроса, недолговечны.</span><span class="sxs-lookup"><span data-stu-id="98836-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="98836-121">[Одинодвий](xref:fundamentals/dependency-injection#service-lifetimes) уровень применения обычно мигрируют в поколение 2.</span><span class="sxs-lookup"><span data-stu-id="98836-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="98836-122">При запуске приложения ASP.NET Core GC:</span><span class="sxs-lookup"><span data-stu-id="98836-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="98836-123">Зарезервирует некоторую память для начальных сегментов кучи.</span><span class="sxs-lookup"><span data-stu-id="98836-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="98836-124">Фиксирует небольшую часть памяти при загрузке времени выполнения.</span><span class="sxs-lookup"><span data-stu-id="98836-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="98836-125">Предыдущие выделения памяти выполняются по причинам производительности.</span><span class="sxs-lookup"><span data-stu-id="98836-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="98836-126">Преимущество производительности происходит от кучи сегментов в смежной памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="98836-127">Позвоните в GC. Собирать</span><span class="sxs-lookup"><span data-stu-id="98836-127">Call GC.Collect</span></span>

<span data-ttu-id="98836-128">Вызов [GC. Сбор](xref:System.GC.Collect*) явно:</span><span class="sxs-lookup"><span data-stu-id="98836-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="98836-129">**Не** следует делать производственные ASP.NET приложения Core.</span><span class="sxs-lookup"><span data-stu-id="98836-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="98836-130">Полезно при расследовании утечек памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="98836-131">При исследовании, проверяет GC удалил все оборванные объекты из памяти, чтобы память может быть измерена.</span><span class="sxs-lookup"><span data-stu-id="98836-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="98836-132">Анализ использования памяти приложения</span><span class="sxs-lookup"><span data-stu-id="98836-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="98836-133">Выделенные инструменты могут помочь в анализе использования памяти:</span><span class="sxs-lookup"><span data-stu-id="98836-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="98836-134">Подсчет ссылок на объекты</span><span class="sxs-lookup"><span data-stu-id="98836-134">Counting object references</span></span>
- <span data-ttu-id="98836-135">Измерение влияния GC на использование процессора</span><span class="sxs-lookup"><span data-stu-id="98836-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="98836-136">Измерение пространства памяти, используемого для каждого поколения</span><span class="sxs-lookup"><span data-stu-id="98836-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="98836-137">Используйте следующие инструменты для анализа использования памяти:</span><span class="sxs-lookup"><span data-stu-id="98836-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="98836-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): может быть использован на производственных машинах.</span><span class="sxs-lookup"><span data-stu-id="98836-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="98836-139">Анализ использования памяти без отладчика Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98836-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="98836-140">Использование памяти профиля в Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98836-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="98836-141">Обнаружение проблем с памятью</span><span class="sxs-lookup"><span data-stu-id="98836-141">Detecting memory issues</span></span>

<span data-ttu-id="98836-142">Диспетчер задач может быть использован, чтобы получить представление о том, сколько памяти использует ASP.NET приложение.</span><span class="sxs-lookup"><span data-stu-id="98836-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="98836-143">Значение памяти менеджера задачи:</span><span class="sxs-lookup"><span data-stu-id="98836-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="98836-144">Представляет объем памяти, используемой ASP.NET процессом.</span><span class="sxs-lookup"><span data-stu-id="98836-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="98836-145">Включает в себя живые объекты приложения и других потребителей памяти, таких как использование нативной памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="98836-146">Если значение памяти task Manager увеличивается бесконечно и никогда не выравнивается, у приложения есть утечка памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="98836-147">Следующие разделы демонстрируют и объясняют несколько шаблонов использования памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="98836-148">Пример приложения для использования памяти отображения</span><span class="sxs-lookup"><span data-stu-id="98836-148">Sample display memory usage app</span></span>

<span data-ttu-id="98836-149">[Образец приложения MemoryLeak](https://github.com/sebastienros/memoryleak) доступен на GitHub.</span><span class="sxs-lookup"><span data-stu-id="98836-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="98836-150">Приложение MemoryLeak:</span><span class="sxs-lookup"><span data-stu-id="98836-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="98836-151">Включает диагностический контроллер, который собирает данные памяти в режиме реального времени и GC для приложения.</span><span class="sxs-lookup"><span data-stu-id="98836-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="98836-152">Имеет страницу индекса, отображая данные памяти и GC.</span><span class="sxs-lookup"><span data-stu-id="98836-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="98836-153">Страница Индекса обновляется каждую секунду.</span><span class="sxs-lookup"><span data-stu-id="98836-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="98836-154">Содержит контроллер API, который обеспечивает различные шаблоны нагрузки памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="98836-155">Не поддерживается инструмент, однако, он может быть использован для отображения памяти использования моделей ASP.NET основных приложений.</span><span class="sxs-lookup"><span data-stu-id="98836-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="98836-156">Выполнить MemoryLeak.</span><span class="sxs-lookup"><span data-stu-id="98836-156">Run MemoryLeak.</span></span> <span data-ttu-id="98836-157">Выделенная память медленно увеличивается до тех пор, пока не произойдет GC.</span><span class="sxs-lookup"><span data-stu-id="98836-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="98836-158">Память увеличивается, потому что инструмент выделяет пользовательский объект для сбора данных.</span><span class="sxs-lookup"><span data-stu-id="98836-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="98836-159">Следующее изображение показывает страницу индекса MemoryLeak, когда происходит Gen 0 GC.</span><span class="sxs-lookup"><span data-stu-id="98836-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="98836-160">Диаграмма показывает 0 RPS (Запросы в секунду), потому что не были вызваны конечные точки API от контроллера API.</span><span class="sxs-lookup"><span data-stu-id="98836-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![предшествующая диаграмма](memory/_static/0RPS.png)

<span data-ttu-id="98836-162">Диаграмма отображает два значения для использования памяти:</span><span class="sxs-lookup"><span data-stu-id="98836-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="98836-163">Выделено: объем памяти, занимаемой управляемыми объектами</span><span class="sxs-lookup"><span data-stu-id="98836-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="98836-164">[Рабочий набор](/windows/win32/memory/working-set): Набор страниц в виртуальном адресном пространстве процесса, которые в настоящее время обитают в физической памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="98836-165">Показанный рабочий набор — это одно и то же значение, отображаемый менеджером задач.</span><span class="sxs-lookup"><span data-stu-id="98836-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="98836-166">Переходные объекты</span><span class="sxs-lookup"><span data-stu-id="98836-166">Transient objects</span></span>

<span data-ttu-id="98836-167">Следующий API создает экземпляр 10-KB String и возвращает его клиенту.</span><span class="sxs-lookup"><span data-stu-id="98836-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="98836-168">По каждому запросу новый объект выделяется в памяти и записываются в ответ.</span><span class="sxs-lookup"><span data-stu-id="98836-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="98836-169">Строки хранятся как символы UTF-16 в .NET, поэтому каждый символ занимает 2 байта в памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="98836-170">Следующий график генерируется с относительно небольшой нагрузкой, чтобы показать, как распределение памяти влияет на GC.</span><span class="sxs-lookup"><span data-stu-id="98836-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![предшествующая диаграмма](memory/_static/bigstring.png)

<span data-ttu-id="98836-172">Предыдущая диаграмма показывает:</span><span class="sxs-lookup"><span data-stu-id="98836-172">The preceding chart shows:</span></span>

* <span data-ttu-id="98836-173">4K RPS (Запросы в секунду).</span><span class="sxs-lookup"><span data-stu-id="98836-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="98836-174">Коллекции GC поколения 0 происходят примерно каждые две секунды.</span><span class="sxs-lookup"><span data-stu-id="98836-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="98836-175">Рабочий набор является постоянным на уровне примерно 500 МБ.</span><span class="sxs-lookup"><span data-stu-id="98836-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="98836-176">Процессор составляет 12%.</span><span class="sxs-lookup"><span data-stu-id="98836-176">CPU is 12%.</span></span>
* <span data-ttu-id="98836-177">Потребление и высвобождение памяти (через GC) стабильно.</span><span class="sxs-lookup"><span data-stu-id="98836-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="98836-178">Следующая диаграмма взята на максимальной пропускной связи, которая может быть обработана машиной.</span><span class="sxs-lookup"><span data-stu-id="98836-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![предшествующая диаграмма](memory/_static/bigstring2.png)

<span data-ttu-id="98836-180">Предыдущая диаграмма показывает:</span><span class="sxs-lookup"><span data-stu-id="98836-180">The preceding chart shows:</span></span>

* <span data-ttu-id="98836-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="98836-181">22K RPS</span></span>
* <span data-ttu-id="98836-182">Коллекции GC поколения 0 встречаются несколько раз в секунду.</span><span class="sxs-lookup"><span data-stu-id="98836-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="98836-183">Коллекции поколения 1 срабатывают, потому что приложение выделяет значительно больше памяти в секунду.</span><span class="sxs-lookup"><span data-stu-id="98836-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="98836-184">Рабочий набор является постоянным на уровне примерно 500 МБ.</span><span class="sxs-lookup"><span data-stu-id="98836-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="98836-185">Процессор составляет 33%.</span><span class="sxs-lookup"><span data-stu-id="98836-185">CPU is 33%.</span></span>
* <span data-ttu-id="98836-186">Потребление и высвобождение памяти (через GC) стабильно.</span><span class="sxs-lookup"><span data-stu-id="98836-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="98836-187">Процессор (33%) не используется чрезмерно, поэтому сбор мусора может идти в ногу с большим количеством ассигнований.</span><span class="sxs-lookup"><span data-stu-id="98836-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="98836-188">Рабочая станция GC против сервера GC</span><span class="sxs-lookup"><span data-stu-id="98836-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="98836-189">Сборщик мусора .NET имеет два различных режима:</span><span class="sxs-lookup"><span data-stu-id="98836-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="98836-190">**Рабочая станция GC**: Оптимизирована для рабочего стола.</span><span class="sxs-lookup"><span data-stu-id="98836-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="98836-191">**Сервер GC**.</span><span class="sxs-lookup"><span data-stu-id="98836-191">**Server GC**.</span></span> <span data-ttu-id="98836-192">GC по умолчанию для ASP.NET основных приложений.</span><span class="sxs-lookup"><span data-stu-id="98836-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="98836-193">Оптимизирован для сервера.</span><span class="sxs-lookup"><span data-stu-id="98836-193">Optimized for the server.</span></span>

<span data-ttu-id="98836-194">Режим GC может быть установлен явно в файле проекта или в файле *runtimeconfig.json* опубликованного приложения.</span><span class="sxs-lookup"><span data-stu-id="98836-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="98836-195">Следующая разметка `ServerGarbageCollection` показывает настройку в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="98836-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="98836-196">Изменение `ServerGarbageCollection` файла проекта требует перестроения приложения.</span><span class="sxs-lookup"><span data-stu-id="98836-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="98836-197">**Примечание:** Сбор мусора сервера **недоступен** на машинах с одним ядром.</span><span class="sxs-lookup"><span data-stu-id="98836-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="98836-198">Для получения дополнительной информации см. <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="98836-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="98836-199">Следующее изображение показывает профиль памяти под 5K RPS с помощью Рабочей станции GC.</span><span class="sxs-lookup"><span data-stu-id="98836-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![предшествующая диаграмма](memory/_static/workstation.png)

<span data-ttu-id="98836-201">Различия между этой диаграммой и серверной версией значительны:</span><span class="sxs-lookup"><span data-stu-id="98836-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="98836-202">Рабочий набор падает с 500 МБ до 70 МБ.</span><span class="sxs-lookup"><span data-stu-id="98836-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="98836-203">GC делает коллекции поколения 0 несколько раз в секунду вместо каждых двух секунд.</span><span class="sxs-lookup"><span data-stu-id="98836-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="98836-204">GC падает с 300 МБ до 10 МБ.</span><span class="sxs-lookup"><span data-stu-id="98836-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="98836-205">В типичной среде веб-сервера использование процессора важнее памяти, поэтому GC сервера лучше.</span><span class="sxs-lookup"><span data-stu-id="98836-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="98836-206">Если использование памяти является высоким и использование процессора является относительно низким, Workstation GC может быть более performant.</span><span class="sxs-lookup"><span data-stu-id="98836-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="98836-207">Например, высокая плотность хостинга нескольких веб-приложений, где память не хватает.</span><span class="sxs-lookup"><span data-stu-id="98836-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="98836-208">GC с использованием Docker и небольших контейнеров</span><span class="sxs-lookup"><span data-stu-id="98836-208">GC using Docker and small containers</span></span>

<span data-ttu-id="98836-209">Когда несколько контейнеризированных приложений работают на одной машине, Workstation GC может быть более пререформантным, чем Server GC.</span><span class="sxs-lookup"><span data-stu-id="98836-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="98836-210">Для получения дополнительной информации [см. Запуск с сервером GC в небольшом контейнере](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) и [запуск с сервером GC в малом контейнерном сценарии Часть 1 - жесткий предел для GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span><span class="sxs-lookup"><span data-stu-id="98836-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="98836-211">Стойкие ссылки на объекты</span><span class="sxs-lookup"><span data-stu-id="98836-211">Persistent object references</span></span>

<span data-ttu-id="98836-212">GC не может освободить объекты, на которые ссылаются.</span><span class="sxs-lookup"><span data-stu-id="98836-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="98836-213">Объекты, на которые ссылаются, но больше не нужны, приводят к утечке памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="98836-214">Если приложение часто выделяет объекты и не освобождает их после того, как они больше не нужны, использование памяти будет увеличиваться с течением времени.</span><span class="sxs-lookup"><span data-stu-id="98836-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="98836-215">Следующий API создает экземпляр 10-KB String и возвращает его клиенту.</span><span class="sxs-lookup"><span data-stu-id="98836-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="98836-216">Разница с предыдущим примером заключается в том, что на этот экземпляр ссылается статический участник, что означает, что он никогда не доступен для сбора.</span><span class="sxs-lookup"><span data-stu-id="98836-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="98836-217">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="98836-217">The preceding code:</span></span>

* <span data-ttu-id="98836-218">Является примером типичной утечки памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="98836-219">При частых звонках память приложения увеличивается до `OutOfMemory` тех пор, пока процесс не сбоит с поломкой, за исключением.</span><span class="sxs-lookup"><span data-stu-id="98836-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![предшествующая диаграмма](memory/_static/eternal.png)

<span data-ttu-id="98836-221">На предыдущем изображении:</span><span class="sxs-lookup"><span data-stu-id="98836-221">In the preceding image:</span></span>

* <span data-ttu-id="98836-222">Нагрузка `/api/staticstring` тестирования конечная точка вызывает линейное увеличение памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="98836-223">GC пытается освободить память по мере роста давления памяти, вызывая коллекцию поколения 2.</span><span class="sxs-lookup"><span data-stu-id="98836-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="98836-224">GC не может освободить просочивную память.</span><span class="sxs-lookup"><span data-stu-id="98836-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="98836-225">Выделенный и рабочий набор увеличивается со временем.</span><span class="sxs-lookup"><span data-stu-id="98836-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="98836-226">Некоторые сценарии, такие как кэширование, требуют, чтобы ссылки объектов удерживаются до тех пор, пока давление памяти не заставить их быть освобожденным.</span><span class="sxs-lookup"><span data-stu-id="98836-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="98836-227">Класс <xref:System.WeakReference> может быть использован для этого типа кода кэширования.</span><span class="sxs-lookup"><span data-stu-id="98836-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="98836-228">Объект `WeakReference` собирается под давлением памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="98836-229">Реализация <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> использования `WeakReference`по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="98836-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="98836-230">Родные памяти</span><span class="sxs-lookup"><span data-stu-id="98836-230">Native memory</span></span>

<span data-ttu-id="98836-231">Некоторые объекты .NET Core полагаются на родную память.</span><span class="sxs-lookup"><span data-stu-id="98836-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="98836-232">Родные памяти **не** могут быть собраны GC.</span><span class="sxs-lookup"><span data-stu-id="98836-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="98836-233">Объект .NET, использующий нативную память, должен освободить его с помощью родного кода.</span><span class="sxs-lookup"><span data-stu-id="98836-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="98836-234">.NET предоставляет <xref:System.IDisposable> интерфейс, чтобы позволить разработчикам выпустить родную память.</span><span class="sxs-lookup"><span data-stu-id="98836-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="98836-235">Даже <xref:System.IDisposable.Dispose*> если не вызывается, правильно `Dispose` реализованы классы вызова при [запуске finalizer.](/dotnet/csharp/programming-guide/classes-and-structs/destructors)</span><span class="sxs-lookup"><span data-stu-id="98836-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="98836-236">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="98836-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="98836-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) является управляемым классом, поэтому любой экземпляр будет собран в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="98836-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="98836-238">Следующее изображение показывает профиль памяти `fileprovider` при постоянном входе в API.</span><span class="sxs-lookup"><span data-stu-id="98836-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![предшествующая диаграмма](memory/_static/fileprovider.png)

<span data-ttu-id="98836-240">Предыдущая диаграмма показывает очевидную проблему с реализацией этого класса, так как она продолжает увеличивать использование памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="98836-241">Это известная проблема, которая отслеживается в [этом вопросе.](https://github.com/dotnet/aspnetcore/issues/3110)</span><span class="sxs-lookup"><span data-stu-id="98836-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="98836-242">Такая же утечка может произойти в пользовательском коде, одним из следующих:</span><span class="sxs-lookup"><span data-stu-id="98836-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="98836-243">Неправильно выпускать класс.</span><span class="sxs-lookup"><span data-stu-id="98836-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="98836-244">Забыв вызвать `Dispose`метод зависимых объектов, которые должны быть удалены.</span><span class="sxs-lookup"><span data-stu-id="98836-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="98836-245">Куча крупных объектов</span><span class="sxs-lookup"><span data-stu-id="98836-245">Large objects heap</span></span>

<span data-ttu-id="98836-246">Частое распределение памяти/свободные циклы могут фрагментировать память, особенно при выделении больших фрагментов памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="98836-247">Объекты распределяются в смежных блоках памяти.</span><span class="sxs-lookup"><span data-stu-id="98836-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="98836-248">Чтобы уменьшить фрагментацию, когда GC освобождает память, он пытается дефрагментировать его.</span><span class="sxs-lookup"><span data-stu-id="98836-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="98836-249">Этот процесс называется **уплотнение**.</span><span class="sxs-lookup"><span data-stu-id="98836-249">This process is called **compaction**.</span></span> <span data-ttu-id="98836-250">Компишн включает в себя перемещение объектов.</span><span class="sxs-lookup"><span data-stu-id="98836-250">Compaction involves moving objects.</span></span> <span data-ttu-id="98836-251">Перемещение крупных объектов налагает штраф за производительность.</span><span class="sxs-lookup"><span data-stu-id="98836-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="98836-252">По этой причине GC создает специальную зону памяти для _крупных_ объектов, [называемую большой объектной кучи](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span><span class="sxs-lookup"><span data-stu-id="98836-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="98836-253">Объекты, превышаюющие 85 000 байтов (примерно 83 кБ), являются:</span><span class="sxs-lookup"><span data-stu-id="98836-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="98836-254">Размещено на LOH.</span><span class="sxs-lookup"><span data-stu-id="98836-254">Placed on the LOH.</span></span>
* <span data-ttu-id="98836-255">Не уплотнен.</span><span class="sxs-lookup"><span data-stu-id="98836-255">Not compacted.</span></span>
* <span data-ttu-id="98836-256">Собрано во время поколения 2 ГК.</span><span class="sxs-lookup"><span data-stu-id="98836-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="98836-257">Когда LOH полон, GC запустит коллекцию поколения 2.</span><span class="sxs-lookup"><span data-stu-id="98836-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="98836-258">Коллекции поколения 2:</span><span class="sxs-lookup"><span data-stu-id="98836-258">Generation 2 collections:</span></span>

* <span data-ttu-id="98836-259">По своей сути медленно.</span><span class="sxs-lookup"><span data-stu-id="98836-259">Are inherently slow.</span></span>
* <span data-ttu-id="98836-260">Кроме того, понесет расходы на запуск коллекции на всех других поколениях.</span><span class="sxs-lookup"><span data-stu-id="98836-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="98836-261">Следующий код уплотняет LOH немедленно:</span><span class="sxs-lookup"><span data-stu-id="98836-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="98836-262">Смотрите <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> для получения информации о уплотнении LOH.</span><span class="sxs-lookup"><span data-stu-id="98836-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="98836-263">В контейнерах с использованием .NET Core 3.0 и позже LOH автоматически уплотняется.</span><span class="sxs-lookup"><span data-stu-id="98836-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="98836-264">Следующий API, иллюстрирующий такое поведение:</span><span class="sxs-lookup"><span data-stu-id="98836-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="98836-265">На следующем графике показан профиль `/api/loh/84975` памяти вызова конечной точки при максимальной нагрузке:</span><span class="sxs-lookup"><span data-stu-id="98836-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![предшествующая диаграмма](memory/_static/loh1.png)

<span data-ttu-id="98836-267">На следующем графике показан профиль `/api/loh/84976` памяти вызова конечной точки, выделяя *еще один байт:*</span><span class="sxs-lookup"><span data-stu-id="98836-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![предшествующая диаграмма](memory/_static/loh2.png)

<span data-ttu-id="98836-269">Примечание: `byte[]` Структура имеет накладные байты.</span><span class="sxs-lookup"><span data-stu-id="98836-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="98836-270">Вот почему 84 976 байтов вызывает 85000 предел.</span><span class="sxs-lookup"><span data-stu-id="98836-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="98836-271">Сравнение двух предыдущих диаграмм:</span><span class="sxs-lookup"><span data-stu-id="98836-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="98836-272">Рабочий набор схож для обоих сценариев, около 450 МБ.</span><span class="sxs-lookup"><span data-stu-id="98836-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="98836-273">Под запросами LOH (84,975 байтов) показывает в основном коллекции поколения 0.</span><span class="sxs-lookup"><span data-stu-id="98836-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="98836-274">Запросы over LOH генерируют постоянные коллекции поколения 2.</span><span class="sxs-lookup"><span data-stu-id="98836-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="98836-275">Коллекции поколения 2 стоят дорого.</span><span class="sxs-lookup"><span data-stu-id="98836-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="98836-276">Требуется больше процессора и пропускная часть падает почти на 50%.</span><span class="sxs-lookup"><span data-stu-id="98836-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="98836-277">Временные крупные объекты особенно проблематичны, поскольку они вызывают gCs gen2.</span><span class="sxs-lookup"><span data-stu-id="98836-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="98836-278">Для максимальной производительности использование больших объектов должно быть сведено к минимуму.</span><span class="sxs-lookup"><span data-stu-id="98836-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="98836-279">Если возможно, разделите крупные объекты.</span><span class="sxs-lookup"><span data-stu-id="98836-279">If possible, split up large objects.</span></span> <span data-ttu-id="98836-280">Например, [промежуточное](xref:performance/caching/response) программное обеспечение Response Caching в ASP.NET Core разделило записи кэша на блоки менее 85 000 байтов.</span><span class="sxs-lookup"><span data-stu-id="98836-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="98836-281">Следующие ссылки показывают ASP.NET основной подход к поддержанию объектов под пределом LOH:</span><span class="sxs-lookup"><span data-stu-id="98836-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="98836-282">ResponseCaching/Streams/StreamUtilities.cs</span><span class="sxs-lookup"><span data-stu-id="98836-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="98836-283">ОтветКэшинг/MemoryResponseCache.cs</span><span class="sxs-lookup"><span data-stu-id="98836-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="98836-284">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="98836-284">For more information, see:</span></span>

* [<span data-ttu-id="98836-285">Большая куча объектов Нераскрытая</span><span class="sxs-lookup"><span data-stu-id="98836-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="98836-286">Большая объектная куча</span><span class="sxs-lookup"><span data-stu-id="98836-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="98836-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="98836-287">HttpClient</span></span>

<span data-ttu-id="98836-288">Неправильное <xref:System.Net.Http.HttpClient> использование может привести к утечке ресурсов.</span><span class="sxs-lookup"><span data-stu-id="98836-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="98836-289">Системные ресурсы, такие как соединения баз данных, розетки, обработки файлов и т.д.:</span><span class="sxs-lookup"><span data-stu-id="98836-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="98836-290">Более скудны, чем память.</span><span class="sxs-lookup"><span data-stu-id="98836-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="98836-291">Являются более проблематичными при утечке, чем память.</span><span class="sxs-lookup"><span data-stu-id="98836-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="98836-292">Опытные разработчики .NET знают, что они вызывают <xref:System.IDisposable.Dispose*> объекты, которые реализуются. <xref:System.IDisposable></span><span class="sxs-lookup"><span data-stu-id="98836-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="98836-293">Неутилизация объектов, `IDisposable` которые реализуются обычно приводит к утечке памяти или утечке системных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="98836-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="98836-294">`HttpClient``IDisposable`реализует, но **не** должен быть удален на каждом вызове.</span><span class="sxs-lookup"><span data-stu-id="98836-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="98836-295">Скорее, `HttpClient` должны быть использованы повторно.</span><span class="sxs-lookup"><span data-stu-id="98836-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="98836-296">Следующая конечная точка создает `HttpClient` и распоряжается новым экземпляром по каждому запросу:</span><span class="sxs-lookup"><span data-stu-id="98836-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="98836-297">При загрузке регистрируются следующие сообщения об ошибках:</span><span class="sxs-lookup"><span data-stu-id="98836-297">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="98836-298">Несмотря `HttpClient` на то, что экземпляры удалены, фактическое подключение к сети занимает некоторое время, чтобы быть освобожденным операционной системой.</span><span class="sxs-lookup"><span data-stu-id="98836-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="98836-299">Непрерывно создавая новые соединения, происходит _истощение портов._</span><span class="sxs-lookup"><span data-stu-id="98836-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="98836-300">Каждое клиентское соединение требует своего порта клиента.</span><span class="sxs-lookup"><span data-stu-id="98836-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="98836-301">Одним из способов предотвращения исчерпания `HttpClient` порта является повторное использование одного и того же экземпляра:</span><span class="sxs-lookup"><span data-stu-id="98836-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="98836-302">Экземпляр `HttpClient` освобождается, когда приложение останавливается.</span><span class="sxs-lookup"><span data-stu-id="98836-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="98836-303">Этот пример показывает, что не каждый одноразовый ресурс должен быть удален после каждого использования.</span><span class="sxs-lookup"><span data-stu-id="98836-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="98836-304">Смотрите следующий способ лучшего способа обработки жизни экземпляра: `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="98836-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="98836-305">Управление HttpClient и временем существования</span><span class="sxs-lookup"><span data-stu-id="98836-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="98836-306">Блог фабрики HTTPClient</span><span class="sxs-lookup"><span data-stu-id="98836-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="98836-307">Объединение объектов</span><span class="sxs-lookup"><span data-stu-id="98836-307">Object pooling</span></span>

<span data-ttu-id="98836-308">Предыдущий пример показал, как `HttpClient` экземпляр может быть статичен и повторно использован всеми запросами.</span><span class="sxs-lookup"><span data-stu-id="98836-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="98836-309">Повторное использование предотвращает иссяке ресурсы.</span><span class="sxs-lookup"><span data-stu-id="98836-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="98836-310">Объединение объектов:</span><span class="sxs-lookup"><span data-stu-id="98836-310">Object pooling:</span></span>

* <span data-ttu-id="98836-311">Использует шаблон повторного использования.</span><span class="sxs-lookup"><span data-stu-id="98836-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="98836-312">Предназначен для объектов, которые являются дорогостоящими для создания.</span><span class="sxs-lookup"><span data-stu-id="98836-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="98836-313">Пул представляет собой набор предварительно инициализированных объектов, которые могут быть зарезервированы и выпущены между потоками.</span><span class="sxs-lookup"><span data-stu-id="98836-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="98836-314">Пулы могут определять правила распределения, такие как ограничения, предопределенные размеры или темпы роста.</span><span class="sxs-lookup"><span data-stu-id="98836-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="98836-315">Пакет NuGet [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) содержит классы, которые помогают управлять такими пулами.</span><span class="sxs-lookup"><span data-stu-id="98836-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="98836-316">Следующая конечная точка API `byte` мгновенно выполняет буфер, наполненный случайными числами по каждому запросу:</span><span class="sxs-lookup"><span data-stu-id="98836-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="98836-317">Следующий отображение диаграммы, вызывающее предыдущий API с умеренной нагрузкой:</span><span class="sxs-lookup"><span data-stu-id="98836-317">The following chart display calling the preceding API with moderate load:</span></span>

![предшествующая диаграмма](memory/_static/array.png)

<span data-ttu-id="98836-319">В предыдущем графике коллекции поколения 0 происходят примерно раз в секунду.</span><span class="sxs-lookup"><span data-stu-id="98836-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="98836-320">Предыдущий код можно оптимизировать `byte` путем объединения буфера с помощью [ArrayPool\<T>. ](xref:System.Buffers.ArrayPool`1)</span><span class="sxs-lookup"><span data-stu-id="98836-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="98836-321">Статический экземпляр используется повторно по запросам.</span><span class="sxs-lookup"><span data-stu-id="98836-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="98836-322">Отличие этого подхода заключается в том, что объединенный объект возвращается из API.</span><span class="sxs-lookup"><span data-stu-id="98836-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="98836-323">Это означает:</span><span class="sxs-lookup"><span data-stu-id="98836-323">That means:</span></span>

* <span data-ttu-id="98836-324">Объект находится вне вашего контроля, как только вы вернетесь из метода.</span><span class="sxs-lookup"><span data-stu-id="98836-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="98836-325">Вы не можете освободить объект.</span><span class="sxs-lookup"><span data-stu-id="98836-325">You can't release the object.</span></span>

<span data-ttu-id="98836-326">Для настройки утилизации объекта:</span><span class="sxs-lookup"><span data-stu-id="98836-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="98836-327">Инкапсулировать объединенный массив в одноразовый объект.</span><span class="sxs-lookup"><span data-stu-id="98836-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="98836-328">Зарегистрируйте объединенный объект с [помощью httpContext.Response.RegisterFordispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="98836-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="98836-329">`RegisterForDispose`будет заботиться `Dispose`о вызове на целевой объект, так что он только выпущен, когда запрос HTTP завершен.</span><span class="sxs-lookup"><span data-stu-id="98836-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="98836-330">Применение той же нагрузки, что и необъединенная версия, приводит к следующему графику:</span><span class="sxs-lookup"><span data-stu-id="98836-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![предшествующая диаграмма](memory/_static/pooledarray.png)

<span data-ttu-id="98836-332">Основное отличие выделяется байтами, и, как следствие, гораздо меньше коллекций поколения 0.</span><span class="sxs-lookup"><span data-stu-id="98836-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98836-333">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="98836-333">Additional resources</span></span>

* [<span data-ttu-id="98836-334">Сборка мусора</span><span class="sxs-lookup"><span data-stu-id="98836-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="98836-335">Понимание различных режимов GC с помощью конпартайзера</span><span class="sxs-lookup"><span data-stu-id="98836-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="98836-336">Большая куча объектов Нераскрытая</span><span class="sxs-lookup"><span data-stu-id="98836-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="98836-337">Большая объектная куча</span><span class="sxs-lookup"><span data-stu-id="98836-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
