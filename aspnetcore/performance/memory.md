---
title: Управление памятью и шаблоны в ASP.NET Core
author: rick-anderson
description: Узнайте, как осуществляется управление памятью в ASP.NET Core и как работает сборщик мусора (GC).
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/memory
ms.openlocfilehash: 7f1d20687f6dd588e125acf3815815c2bcf0cd04
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722687"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="e4bdd-103">Управление памятью и сборка мусора (GC) в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e4bdd-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="e4bdd-104">[Сéбастиен рос](https://github.com/sebastienros) и [Рик Андерсон (](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e4bdd-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e4bdd-105">Управление памятью является сложной задачей даже в управляемой платформе, такой как .NET.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="e4bdd-106">Анализ и понимание проблем с памятью может быть непростой задачей.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="e4bdd-107">В этой статье:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-107">This article:</span></span>

* <span data-ttu-id="e4bdd-108">Было вызвано многими *утечками памяти* и *отсутствием проблем в работе GC* .</span><span class="sxs-lookup"><span data-stu-id="e4bdd-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="e4bdd-109">Большинство этих проблем было вызвано не пониманием того, как потребление памяти работает в .NET Core, или не понимание того, как оно измеряется.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="e4bdd-110">Демонстрирует проблемное использование памяти и предлагает альтернативные подходы.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="e4bdd-111">Принцип работы сборки мусора в .NET Core</span><span class="sxs-lookup"><span data-stu-id="e4bdd-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="e4bdd-112">Сборщик мусора выделяет сегменты кучи, в которых каждый сегмент является непрерывным диапазоном памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="e4bdd-113">Объекты, помещенные в кучу, делятся на три поколения: 0, 1 или 2.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="e4bdd-114">Поколение определяет частоту, с которой сборщик мусора пытается освободить память на управляемых объектах, на которые больше не ссылается приложение.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="e4bdd-115">Более низкие номера поколений чаще всего являются GC.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="e4bdd-116">Объекты перемещаются из одного поколения в другое в зависимости от времени их существования.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="e4bdd-117">Как только объекты проводятся дольше, они перемещаются в более высокое поколение.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="e4bdd-118">Как упоминалось ранее, более высокие поколения являются сборщиком мусора менее часто.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="e4bdd-119">Кратковременные объекты с краткосрочным временем существования всегда остаются в поколении 0.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="e4bdd-120">Например, кратковременно используются объекты, на которые имеются ссылки в течение жизненного цикла веб-запроса.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="e4bdd-121">[Одноэлементные](xref:fundamentals/dependency-injection#service-lifetimes) экземпляры уровня приложения обычно переносятся в поколение 2.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="e4bdd-122">При запуске ASP.NET Coreного приложения сборщик мусора выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="e4bdd-123">Резервирует некоторый объем памяти для начальных сегментов кучи.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="e4bdd-124">Фиксирует небольшую часть памяти при загрузке среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="e4bdd-125">Предыдущие выделения памяти выполняются по соображениям производительности.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="e4bdd-126">Преимущество производительности состоит из сегментов кучи в непрерывной памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="e4bdd-127">Вызовите GC. Получение</span><span class="sxs-lookup"><span data-stu-id="e4bdd-127">Call GC.Collect</span></span>

<span data-ttu-id="e4bdd-128">Вызов [GC. Собирайте](xref:System.GC.Collect*) явным образом:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="e4bdd-129">**Не** следует делать в производственных ASP.NET Core приложениях.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="e4bdd-130">Полезен при исследовании утечек памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="e4bdd-131">При исследовании проверяется, что сборщик мусора удалил все висячие объекты из памяти, чтобы можно было измерять память.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="e4bdd-132">Анализ использования памяти приложением</span><span class="sxs-lookup"><span data-stu-id="e4bdd-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="e4bdd-133">Специальные средства могут помочь в анализе использования памяти:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="e4bdd-134">Подсчет ссылок на объекты</span><span class="sxs-lookup"><span data-stu-id="e4bdd-134">Counting object references</span></span>
- <span data-ttu-id="e4bdd-135">Измерение степени влияния сборки мусора на использование ЦП</span><span class="sxs-lookup"><span data-stu-id="e4bdd-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="e4bdd-136">Измерение пространства памяти, используемого для каждого поколения</span><span class="sxs-lookup"><span data-stu-id="e4bdd-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="e4bdd-137">Используйте следующие средства для анализа использования памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="e4bdd-138">[DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace): может использоваться на рабочих компьютерах.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="e4bdd-139">Анализ использования памяти без отладчика Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e4bdd-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="e4bdd-140">Использование памяти профиля в Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e4bdd-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="e4bdd-141">Обнаружение проблем с памятью</span><span class="sxs-lookup"><span data-stu-id="e4bdd-141">Detecting memory issues</span></span>

<span data-ttu-id="e4bdd-142">Диспетчер задач можно использовать для получения представления о том, сколько памяти использует приложение ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="e4bdd-143">Значение памяти диспетчера задач:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="e4bdd-144">Представляет объем памяти, используемый процессом ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="e4bdd-145">Включает в себя собственные объекты приложения и другие потребители памяти, такие как использование памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="e4bdd-146">Если значение памяти диспетчера задач постоянно увеличивается и никогда не происходит, в приложении возникает утечка памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="e4bdd-147">В следующих разделах показаны и описаны некоторые закономерности использования памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="e4bdd-148">Пример приложения для просмотра использования памяти</span><span class="sxs-lookup"><span data-stu-id="e4bdd-148">Sample display memory usage app</span></span>

<span data-ttu-id="e4bdd-149">[Пример приложения меморилеак](https://github.com/sebastienros/memoryleak) доступен на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="e4bdd-150">Приложение Меморилеак:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="e4bdd-151">Включает диагностический контроллер, собирающий память в режиме реального времени и данные сборки мусора для приложения.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="e4bdd-152">Содержит страницу индекса, которая отображает память и данные GC.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="e4bdd-153">Страница индекса обновляется каждую секунду.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="e4bdd-154">Содержит контроллер API, который предоставляет различные шаблоны нагрузки для памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="e4bdd-155">Не является поддерживаемым средством, однако его можно использовать для просмотра шаблонов использования памяти ASP.NET Core приложений.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="e4bdd-156">Запустите Меморилеак.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-156">Run MemoryLeak.</span></span> <span data-ttu-id="e4bdd-157">Выделенная память медленно увеличивается, пока не произойдет GC.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="e4bdd-158">Память увеличивается, поскольку средство выделяет пользовательский объект для записи данных.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="e4bdd-159">На следующем рисунке показана страница индекса Меморилеак при возникновении сборки мусора Gen 0.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="e4bdd-160">На диаграмме показано 0 RPS (запросов в секунду), так как не были вызваны конечные точки API из контроллера API.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![Предыдущая диаграмма](memory/_static/0RPS.png)

<span data-ttu-id="e4bdd-162">На диаграмме показаны два значения использования памяти:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="e4bdd-163">Выделение: объем памяти, занятой управляемыми объектами</span><span class="sxs-lookup"><span data-stu-id="e4bdd-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="e4bdd-164">[Рабочий набор](/windows/win32/memory/working-set): набор страниц в виртуальном адресном пространстве процесса, которые в настоящий момент находятся в физической памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="e4bdd-165">Отображаемый рабочий набор имеет то же значение, что и диспетчер задач.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="e4bdd-166">Временные объекты</span><span class="sxs-lookup"><span data-stu-id="e4bdd-166">Transient objects</span></span>

<span data-ttu-id="e4bdd-167">Следующий API создает экземпляр строки размером 10 КБ и возвращает его клиенту.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="e4bdd-168">При каждом запросе новый объект выделяется в памяти и записывается в ответ.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="e4bdd-169">Строки хранятся в .NET в виде символов UTF-16, поэтому каждый символ занимает 2 байта в памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="e4bdd-170">Следующая диаграмма создается с относительно небольшой нагрузкой в, чтобы продемонстрировать, как это влияет на выделение памяти сборщиком мусора.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![Предыдущая диаграмма](memory/_static/bigstring.png)

<span data-ttu-id="e4bdd-172">На предыдущей диаграмме показано следующее:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-172">The preceding chart shows:</span></span>

* <span data-ttu-id="e4bdd-173">4 КБ (запросов в секунду).</span><span class="sxs-lookup"><span data-stu-id="e4bdd-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="e4bdd-174">Коллекции GC поколения 0 происходят каждые две секунды.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="e4bdd-175">Рабочий набор является константой приблизительно 500 МБ.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="e4bdd-176">ЦП равен 12%.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-176">CPU is 12%.</span></span>
* <span data-ttu-id="e4bdd-177">Потребление памяти и выпуск (через GC) стабильны.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="e4bdd-178">На следующей диаграмме приведена максимальная пропускная способность, которую может обработать компьютер.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![Предыдущая диаграмма](memory/_static/bigstring2.png)

<span data-ttu-id="e4bdd-180">На предыдущей диаграмме показано следующее:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-180">The preceding chart shows:</span></span>

* <span data-ttu-id="e4bdd-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="e4bdd-181">22K RPS</span></span>
* <span data-ttu-id="e4bdd-182">Коллекции GC поколения 0 встречаются несколько раз в секунду.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="e4bdd-183">Сборки поколения 1 запускаются, так как приложение выделяет значительно больше памяти в секунду.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="e4bdd-184">Рабочий набор является константой приблизительно 500 МБ.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="e4bdd-185">ЦП равен 33%.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-185">CPU is 33%.</span></span>
* <span data-ttu-id="e4bdd-186">Потребление памяти и выпуск (через GC) стабильны.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="e4bdd-187">ЦП (33%) не используется, поэтому сборка мусора может иметь большое количество выделений.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="e4bdd-188">Сборщик мусора рабочей станции и сервер GC</span><span class="sxs-lookup"><span data-stu-id="e4bdd-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="e4bdd-189">Сборщик мусора .NET имеет два разных режима:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="e4bdd-190">**Сборщик мусора рабочей станции**: оптимизирован для настольных систем.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="e4bdd-191">**GC сервера**.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-191">**Server GC**.</span></span> <span data-ttu-id="e4bdd-192">Глобальный каталог по умолчанию для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="e4bdd-193">Оптимизировано для сервера.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-193">Optimized for the server.</span></span>

<span data-ttu-id="e4bdd-194">Режим GC можно задать явно в файле проекта или в *runtimeconfig.js* файла опубликованного приложения.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="e4bdd-195">В следующей разметке показан параметр `ServerGarbageCollection` в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="e4bdd-196">`ServerGarbageCollection`Для изменения в файле проекта требуется Перестроение приложения.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="e4bdd-197">**Примечание.** Сборка мусора сервера **недоступна** на компьютерах с одним ядром.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="e4bdd-198">Для получения дополнительной информации см. <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="e4bdd-199">На следующем рисунке показан профиль памяти в 5 КБ RPS с помощью сборщика мусора рабочей станции.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![Предыдущая диаграмма](memory/_static/workstation.png)

<span data-ttu-id="e4bdd-201">Различия между этой диаграммой и версией сервера существенны:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="e4bdd-202">Рабочий набор удаляется с 500 МБ до 70 МБ.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="e4bdd-203">Сборщик мусора выделает коллекции поколения 0 несколько раз в секунду, а не каждые две секунды.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="e4bdd-204">GC перепадает с 300 МБ на 10 МБ.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="e4bdd-205">В типичной среде веб-сервера загрузка ЦП важнее, чем память, поэтому сервер GC лучше.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="e4bdd-206">Если интенсивность использования памяти высока и загрузка ЦП относительно низкая, то сборщик мусора рабочей станции может быть более производительным.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="e4bdd-207">Например, высокая плотность размещения нескольких веб-приложений, где недостаточно памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="e4bdd-208">GC с использованием DOCKER и небольших контейнеров</span><span class="sxs-lookup"><span data-stu-id="e4bdd-208">GC using Docker and small containers</span></span>

<span data-ttu-id="e4bdd-209">Если на одном компьютере выполняется несколько контейнерных приложений, сборщик мусора рабочей станции может быть более предустановлен, чем сервер GC.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="e4bdd-210">Дополнительные сведения см. в разделе [Работа с GC сервера в небольшом контейнере](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) и [Работа с GC сервера в небольшом сценарии, часть 1 — жесткое ограничение для кучи сборщика мусора](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span><span class="sxs-lookup"><span data-stu-id="e4bdd-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="e4bdd-211">Постоянные ссылки на объекты</span><span class="sxs-lookup"><span data-stu-id="e4bdd-211">Persistent object references</span></span>

<span data-ttu-id="e4bdd-212">Сборщику мусора не удается освободить объекты, на которые имеются ссылки.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="e4bdd-213">Объекты, на которые имеются ссылки, но которые больше не нужны, приводят к утечке памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="e4bdd-214">Если приложение часто выделяет объекты и не может освободить их после того, как они больше не нужны, использование памяти увеличится со временем.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="e4bdd-215">Следующий API создает экземпляр строки размером 10 КБ и возвращает его клиенту.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="e4bdd-216">Разница с предыдущим примером заключается в том, что на этот экземпляр ссылается статический член, что означает, что он никогда не доступен для коллекции.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

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

<span data-ttu-id="e4bdd-217">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-217">The preceding code:</span></span>

* <span data-ttu-id="e4bdd-218">— Пример типичной утечки памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="e4bdd-219">При частом вызове происходит увеличение объема памяти приложения до аварийного завершения процесса с `OutOfMemory` исключением.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![Предыдущая диаграмма](memory/_static/eternal.png)

<span data-ttu-id="e4bdd-221">На предыдущем рисунке:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-221">In the preceding image:</span></span>

* <span data-ttu-id="e4bdd-222">Нагрузочное тестирование `/api/staticstring` конечной точки вызывает линейное увеличение памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="e4bdd-223">Сборщик мусора пытается освободить память по мере роста нехватки памяти путем вызова сборки поколения 2.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="e4bdd-224">Сборщику мусора не удается освободить утечку памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="e4bdd-225">Увеличение выделенного и рабочего набора с учетом времени.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="e4bdd-226">В некоторых сценариях, таких как кэширование, требуется, чтобы ссылки на объекты удерживались до тех пор, пока не будет принудительно снята нагрузка на память.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="e4bdd-227"><xref:System.WeakReference>Класс может использоваться для этого типа кода кэширования.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="e4bdd-228">`WeakReference`Объект собирается при нехватке памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="e4bdd-229">Используемая по умолчанию реализация <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference` .</span><span class="sxs-lookup"><span data-stu-id="e4bdd-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="e4bdd-230">Собственная память</span><span class="sxs-lookup"><span data-stu-id="e4bdd-230">Native memory</span></span>

<span data-ttu-id="e4bdd-231">Некоторые объекты .NET Core основываются на собственной памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="e4bdd-232">Встроенная память **не** может быть СОБРАНа сборщиком мусора.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="e4bdd-233">Объект .NET, использующий собственную память, должен освободить его с помощью машинного кода.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="e4bdd-234">.NET предоставляет <xref:System.IDisposable> интерфейс, позволяющий разработчикам освобождать собственную память.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="e4bdd-235">Даже если <xref:System.IDisposable.Dispose*> не вызывается, правильно реализованные классы вызываются `Dispose` при запуске [метода завершения](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .</span><span class="sxs-lookup"><span data-stu-id="e4bdd-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="e4bdd-236">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="e4bdd-237">[Фисикалфилепровидер](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) является управляемым классом, поэтому в конце запроса будет собираться любой экземпляр.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="e4bdd-238">На следующем рисунке показан профиль памяти при постоянном вызове `fileprovider` API.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![Предыдущая диаграмма](memory/_static/fileprovider.png)

<span data-ttu-id="e4bdd-240">На предыдущей диаграмме показана очевидная проблема с реализацией этого класса, так как она обеспечивает увеличение использования памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="e4bdd-241">Это известная проблема, которая отслеживается в [этой проблеме](https://github.com/dotnet/aspnetcore/issues/3110).</span><span class="sxs-lookup"><span data-stu-id="e4bdd-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="e4bdd-242">Такая же утечка может произойти в пользовательском коде одним из следующих:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="e4bdd-243">Не освобождайте класс должным образом.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="e4bdd-244">Не `Dispose` удается вызвать метод зависимых объектов, которые должны быть удалены.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="e4bdd-245">Куча больших объектов</span><span class="sxs-lookup"><span data-stu-id="e4bdd-245">Large objects heap</span></span>

<span data-ttu-id="e4bdd-246">Частые циклы выделения и освобождения памяти могут фрагментировать память, особенно при выделении больших блоков памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="e4bdd-247">Объекты выделяются в смежных блоках памяти.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="e4bdd-248">Чтобы устранить фрагментацию, когда сборщик мусора освобождает память, он пытается выполнить дефрагментацию.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-248">To mitigate fragmentation, when the GC frees memory, it tries to defragment it.</span></span> <span data-ttu-id="e4bdd-249">Этот процесс называется **сжатием**.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-249">This process is called **compaction**.</span></span> <span data-ttu-id="e4bdd-250">Сжатие включает в себя перемещение объектов.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-250">Compaction involves moving objects.</span></span> <span data-ttu-id="e4bdd-251">Перемещение больших объектов накладывает снижение производительности.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="e4bdd-252">По этой причине сборщик мусора создает специальную зону памяти для _больших_ объектов, которая называется [кучей больших объектов](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span><span class="sxs-lookup"><span data-stu-id="e4bdd-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="e4bdd-253">Объекты размером более 85 000 байт (примерно 83 КБ):</span><span class="sxs-lookup"><span data-stu-id="e4bdd-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="e4bdd-254">Размещается в LOH.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-254">Placed on the LOH.</span></span>
* <span data-ttu-id="e4bdd-255">Не сжато.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-255">Not compacted.</span></span>
* <span data-ttu-id="e4bdd-256">Собраны во время сборки мусора поколения 2.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="e4bdd-257">Когда LOH заполнена, сборщик мусора запускает сборку поколения 2.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="e4bdd-258">Коллекции поколения 2:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-258">Generation 2 collections:</span></span>

* <span data-ttu-id="e4bdd-259">По сути являются медленными.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-259">Are inherently slow.</span></span>
* <span data-ttu-id="e4bdd-260">Кроме того, следует взимать затраты на активацию коллекции во всех других поколениях.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="e4bdd-261">Следующий код немедленно сжимает LOH:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="e4bdd-262"><xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>Дополнительные сведения о сжатии LOH см. в разделе.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="e4bdd-263">В контейнерах, использующих .NET Core 3,0 и более поздних версий, LOH автоматически сжимается.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="e4bdd-264">Следующий API демонстрирует такое поведение:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="e4bdd-265">На следующей диаграмме показан профиль памяти для вызова `/api/loh/84975` конечной точки в разделе максимальная нагрузка:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![Предыдущая диаграмма](memory/_static/loh1.png)

<span data-ttu-id="e4bdd-267">На следующей диаграмме показан профиль памяти для вызова `/api/loh/84976` конечной точки, который выделяет еще *один байт*:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![Предыдущая диаграмма](memory/_static/loh2.png)

<span data-ttu-id="e4bdd-269">Примечание. в `byte[]` структуре содержится дополнительная нагрузка на байты.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="e4bdd-270">Вот почему 84 976 байта вызывает ограничение 85 000.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="e4bdd-271">Сравнение двух предыдущих диаграмм:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="e4bdd-272">Рабочий набор аналогичен обоим сценариям, примерно 450 МБ.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="e4bdd-273">В разделе запросы LOH (84 975 байт) показаны в основном коллекции поколения 0.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="e4bdd-274">При чрезмерном запросе LOH генерируются коллекции констант поколения 2.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="e4bdd-275">Коллекции поколения 2 являются дорогостоящими.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="e4bdd-276">Требуется больше ресурсов ЦП, и пропускная способность падает почти на 50%.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="e4bdd-277">Временные крупные объекты особенно проблематичны, так как они вызывают Gen2 GC.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="e4bdd-278">Для максимальной производительности использование большого объекта должно быть минимальным.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="e4bdd-279">Если возможно, разделите большие объекты.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-279">If possible, split up large objects.</span></span> <span data-ttu-id="e4bdd-280">Например, по промежуточного слоя [кэширования ответа](xref:performance/caching/response) в ASP.NET Core Разделите записи кэша на блоки менее 85 000 байт.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="e4bdd-281">Следующие ссылки показывают ASP.NET Core подходе к хранению объектов под пределами LOH:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="e4bdd-282">Респонсекачинг/Streams/Стреамутилитиес. CS</span><span class="sxs-lookup"><span data-stu-id="e4bdd-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="e4bdd-283">Респонсекачинг/Мемориреспонсекаче. CS</span><span class="sxs-lookup"><span data-stu-id="e4bdd-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="e4bdd-284">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-284">For more information, see:</span></span>

* [<span data-ttu-id="e4bdd-285">Обнаружена куча больших объектов</span><span class="sxs-lookup"><span data-stu-id="e4bdd-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="e4bdd-286">Куча больших объектов</span><span class="sxs-lookup"><span data-stu-id="e4bdd-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="e4bdd-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="e4bdd-287">HttpClient</span></span>

<span data-ttu-id="e4bdd-288">Неправильное использование <xref:System.Net.Http.HttpClient> может привести к утечке ресурсов.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="e4bdd-289">Системные ресурсы, такие как подключения к базам данных, сокеты, дескрипторы файлов и т. д.:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="e4bdd-290">Больше, чем память.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="e4bdd-291">Более проблематично, если утечка по сравнению с памятью.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="e4bdd-292">Опытным разработчикам .NET известна возможность вызова <xref:System.IDisposable.Dispose*> для объектов, реализующих <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="e4bdd-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="e4bdd-293">Удаление объектов, которые реализуют, `IDisposable` как правило, приводит к утечке памяти или утечке системных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="e4bdd-294">`HttpClient` реализует `IDisposable` , но **не** следует удалять при каждом вызове.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="e4bdd-295">Вместо этого `HttpClient` следует использовать повторно.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="e4bdd-296">Следующая Конечная точка создает и уничтожает новый  `HttpClient` экземпляр при каждом запросе:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

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

<span data-ttu-id="e4bdd-297">В разделе Загрузка регистрируются следующие сообщения об ошибках:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-297">Under load, the following error messages are logged:</span></span>

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

<span data-ttu-id="e4bdd-298">Несмотря на то `HttpClient` , что экземпляры удалены, фактическое сетевое подключение занимает некоторое время, выпуская операционной системой.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="e4bdd-299">При непрерывном создании новых соединений происходит _нехватка портов_ .</span><span class="sxs-lookup"><span data-stu-id="e4bdd-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="e4bdd-300">Для каждого клиентского соединения требуется свой порт клиента.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="e4bdd-301">Одним из способов предотвращения нехватки портов является повторное использование того же `HttpClient` экземпляра:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="e4bdd-302">`HttpClient`Экземпляр освобождается при остановке приложения.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="e4bdd-303">В этом примере показано, что не каждый удаляемый ресурс должен быть удален после каждого использования.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="e4bdd-304">Дополнительные сведения об обработке времени существования экземпляра см. в следующих статьях `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="e4bdd-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="e4bdd-305">Управление HttpClient и временем существования</span><span class="sxs-lookup"><span data-stu-id="e4bdd-305">HttpClient and lifetime management</span></span>](../fundamentals/http-requests.md#httpclient-and-lifetime-management)
* [<span data-ttu-id="e4bdd-306">Блог фабрики HTTPClient</span><span class="sxs-lookup"><span data-stu-id="e4bdd-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="e4bdd-307">Использование пулов объектов</span><span class="sxs-lookup"><span data-stu-id="e4bdd-307">Object pooling</span></span>

<span data-ttu-id="e4bdd-308">В предыдущем примере показано, как `HttpClient` экземпляр можно сделать статическим и повторно использовать во всех запросах.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="e4bdd-309">Повторное использование не доблокирует ресурсы.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="e4bdd-310">Объединение объектов в пул:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-310">Object pooling:</span></span>

* <span data-ttu-id="e4bdd-311">Использует шаблон повторного использования.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="e4bdd-312">Предназначен для объектов, которые могут создаваться дорого.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="e4bdd-313">Пул — это коллекция предварительно инициализированных объектов, которые можно зарезервировать и освободить в потоках.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="e4bdd-314">Пулы могут определять правила распределения, такие как ограничения, предопределенные размеры или скорость роста.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="e4bdd-315">Пакет NuGet [Microsoft. Extensions. обжектпул](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) содержит классы, помогающие управлять такими пулами.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="e4bdd-316">Следующая Конечная точка API создает экземпляр `byte` буфера, который заполняется случайными числами для каждого запроса:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

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

<span data-ttu-id="e4bdd-317">На следующей диаграмме показан вызов предыдущего API с умеренной нагрузкой:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-317">The following chart display calling the preceding API with moderate load:</span></span>

![Предыдущая диаграмма](memory/_static/array.png)

<span data-ttu-id="e4bdd-319">На приведенной выше диаграмме сборки поколения 0 происходят приблизительно в секунду.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="e4bdd-320">Предыдущий код можно оптимизировать путем объединения `byte` буфера с помощью [ \<T> аррайпул](xref:System.Buffers.ArrayPool`1).</span><span class="sxs-lookup"><span data-stu-id="e4bdd-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="e4bdd-321">Статический экземпляр повторно используется в запросах.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="e4bdd-322">В отличие от этого подхода, объект poold возвращается из API.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="e4bdd-323">Это означает:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-323">That means:</span></span>

* <span data-ttu-id="e4bdd-324">Объект находится вне элемента управления, как только вы вернетесь из метода.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="e4bdd-325">Вы не можете освободить объект.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-325">You can't release the object.</span></span>

<span data-ttu-id="e4bdd-326">Чтобы настроить удаление объекта, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="e4bdd-327">Инкапсулирует массив в пуле в удаляемый объект.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="e4bdd-328">Зарегистрируйте объект poold с помощью [HttpContext. Response. регистерфордиспосе](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="e4bdd-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="e4bdd-329">`RegisterForDispose` будет выполнять вызов `Dispose` на целевом объекте, чтобы он выпускался только после завершения HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

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

<span data-ttu-id="e4bdd-330">Применение той же нагрузки, что и версия без пула, приводит к следующей диаграмме:</span><span class="sxs-lookup"><span data-stu-id="e4bdd-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![Предыдущая диаграмма](memory/_static/pooledarray.png)

<span data-ttu-id="e4bdd-332">Основное различие состоит в том, что выделяется байты, и в итоге число сборок поколения 0 будет значительно меньше.</span><span class="sxs-lookup"><span data-stu-id="e4bdd-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4bdd-333">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e4bdd-333">Additional resources</span></span>

* [<span data-ttu-id="e4bdd-334">Сборка мусора</span><span class="sxs-lookup"><span data-stu-id="e4bdd-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="e4bdd-335">Основные сведения о различных режимах сборки мусора с помощью визуализатора параллелизма</span><span class="sxs-lookup"><span data-stu-id="e4bdd-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="e4bdd-336">Обнаружена куча больших объектов</span><span class="sxs-lookup"><span data-stu-id="e4bdd-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="e4bdd-337">Куча больших объектов</span><span class="sxs-lookup"><span data-stu-id="e4bdd-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)