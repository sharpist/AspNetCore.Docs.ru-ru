---
title: Шаблон параметров в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать шаблон параметров для представления групп связанных параметров в приложениях ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/options
ms.openlocfilehash: 300b26c198e6ea07fe83af8fb9ae967e814396fb
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408361"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="43554-103">Шаблон параметров в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43554-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="43554-104">Авторы: [Кирк Ларкин (Kirk Larkin)](https://twitter.com/serpent5) и [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="43554-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="43554-105">Шаблон параметров использует классы для обеспечения строго типизированного доступа к группам связанных параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="43554-106">Когда [параметры конфигурации](xref:fundamentals/configuration/index) изолируются по сценарию в отдельных классах, в приложениях соблюдаются два важных принципа программной инженерии.</span><span class="sxs-lookup"><span data-stu-id="43554-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="43554-107">[Принцип разделения интерфейса (ISP) или инкапсуляция.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) Сценарии (классы), которые зависят от параметров конфигурации, зависят только от используемых ими параметров конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="43554-108">[Разделение областей ответственности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). Параметры для разных частей приложения не зависят друг от друга и не связаны друг с другом.</span><span class="sxs-lookup"><span data-stu-id="43554-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="43554-109">В параметрах также предусмотрен механизм для проверки данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="43554-110">Дополнительные сведения см. в разделе [Проверка параметров](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="43554-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="43554-111">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43554-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="43554-112">Привязка иерархической конфигурации</span><span class="sxs-lookup"><span data-stu-id="43554-112">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="43554-113">Интерфейсы параметров</span><span class="sxs-lookup"><span data-stu-id="43554-113">Options interfaces</span></span>

<span data-ttu-id="43554-114"><xref:Microsoft.Extensions.Options.IOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-114"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="43554-115">***Не*** поддерживает:</span><span class="sxs-lookup"><span data-stu-id="43554-115">Does ***not*** support:</span></span>
  * <span data-ttu-id="43554-116">чтение данных конфигурации после запуска приложения;</span><span class="sxs-lookup"><span data-stu-id="43554-116">Reading of configuration data after the app has started.</span></span>
  * <span data-ttu-id="43554-117">[именованные параметры](#named);</span><span class="sxs-lookup"><span data-stu-id="43554-117">[Named options](#named)</span></span>
* <span data-ttu-id="43554-118">Регистрируется в качестве элемента [singleton](xref:fundamentals/dependency-injection#singleton) и может быть внедрен в любое [время существования службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="43554-118">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="43554-119"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-119"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="43554-120">Полезно использовать в сценариях, когда параметры нужно заново вычислять при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="43554-120">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="43554-121">Дополнительные сведения см. в разделе [Использование IOptionsSnapshot для чтения обновленных данных](#ios).</span><span class="sxs-lookup"><span data-stu-id="43554-121">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="43554-122">Регистрируется как [Scoped](xref:fundamentals/dependency-injection#scoped) (с областью), поэтому его нельзя внедрить в службу singleton.</span><span class="sxs-lookup"><span data-stu-id="43554-122">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="43554-123">Поддерживает [именованные параметры](#named).</span><span class="sxs-lookup"><span data-stu-id="43554-123">Supports [named options](#named)</span></span>

<span data-ttu-id="43554-124"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-124"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="43554-125">Используется для извлечения параметров и управления уведомлениями о параметрах для экземпляров `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="43554-125">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="43554-126">Регистрируется в качестве элемента [singleton](xref:fundamentals/dependency-injection#singleton) и может быть внедрен в любое [время существования службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="43554-126">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="43554-127">Поддерживаются следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="43554-127">Supports:</span></span>
  * <span data-ttu-id="43554-128">уведомления об изменениях;</span><span class="sxs-lookup"><span data-stu-id="43554-128">Change notifications</span></span>
  * <span data-ttu-id="43554-129">[именованные параметры](#named-options-support-with-iconfigurenamedoptions);</span><span class="sxs-lookup"><span data-stu-id="43554-129">[Named options](#named-options-support-with-iconfigurenamedoptions)</span></span>
  * <span data-ttu-id="43554-130">[перезагружаемые конфигурации](#ios);</span><span class="sxs-lookup"><span data-stu-id="43554-130">[Reloadable configuration](#ios)</span></span>
  * <span data-ttu-id="43554-131">объявление определенных параметров недействительными (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>).</span><span class="sxs-lookup"><span data-stu-id="43554-131">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="43554-132">Сценарии [пост-конфигурации](#options-post-configuration) позволяют задать или изменить параметры после выполнения всех действий настройки с помощью <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-132">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="43554-133">Интерфейс <xref:Microsoft.Extensions.Options.IOptionsFactory%601> отвечает за создание экземпляров параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-133"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="43554-134">Он имеет единственный метод <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="43554-134">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="43554-135">При реализации по умолчанию принимаются все зарегистрированные интерфейсы <xref:Microsoft.Extensions.Options.IConfigureOptions%601> и <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. Также сначала выполняются все основные настройки, а затем действия после конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-135">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="43554-136">Она различает интерфейсы <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> и <xref:Microsoft.Extensions.Options.IConfigureOptions%601> и вызывает только соответствующий интерфейс.</span><span class="sxs-lookup"><span data-stu-id="43554-136">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="43554-137"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> используется интерфейсом <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> для записи экземпляров `TOptions` в кэш.</span><span class="sxs-lookup"><span data-stu-id="43554-137"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="43554-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> делает экземпляры параметров в мониторе недействительными, что приводит к повторному вычислению значений (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="43554-138">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="43554-139">Значения можно также вводить вручную с помощью <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="43554-139">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="43554-140">Метод <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> используется, если необходимо повторно создать все именованные экземпляры по требованию.</span><span class="sxs-lookup"><span data-stu-id="43554-140">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="43554-141">Использование IOptionsSnapshot для чтения обновленных данных</span><span class="sxs-lookup"><span data-stu-id="43554-141">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="43554-142">С помощью <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> параметры вычисляются один раз на каждый запрос при обращении к ним и кэшируются на все время существования запроса.</span><span class="sxs-lookup"><span data-stu-id="43554-142">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="43554-143">Изменения конфигурации считываются после запуска приложения при использовании поставщиков конфигурации, поддерживающих чтение обновленных значений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-143">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="43554-144">Разница между `IOptionsMonitor` и `IOptionsSnapshot`:</span><span class="sxs-lookup"><span data-stu-id="43554-144">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="43554-145">`IOptionsMonitor` — это [одноэлементная служба](xref:fundamentals/dependency-injection#singleton), которая получает текущие значения параметров в любое время, что особенно полезно в одноэлементных зависимостях.</span><span class="sxs-lookup"><span data-stu-id="43554-145">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="43554-146">`IOptionsSnapshot` — это [служба с заданной областью действия](xref:fundamentals/dependency-injection#scoped), предоставляющая моментальный снимок параметров на момент создания объекта `IOptionsSnapshot<T>`.</span><span class="sxs-lookup"><span data-stu-id="43554-146">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="43554-147">Моментальные снимки параметров предназначены для использования с временными зависимостями и зависимостями с заданной областью действия.</span><span class="sxs-lookup"><span data-stu-id="43554-147">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="43554-148">В приведенном ниже коде используется <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-148">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="43554-149">Следующий код регистрирует экземпляр конфигурации, к которому привязывается `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="43554-149">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="43554-150">В приведенном выше коде изменения в файле конфигурации JSON, внесенные после запуска приложения, считываются.</span><span class="sxs-lookup"><span data-stu-id="43554-150">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="43554-151">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="43554-151">IOptionsMonitor</span></span>

<span data-ttu-id="43554-152">Следующий код регистрирует экземпляр конфигурации, к которому привязывается `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="43554-152">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="43554-153">В следующем примере используется <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-153">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="43554-154">По умолчанию в приведенном выше коде изменения в файле конфигурации JSON, внесенные после запуска приложения, считываются.</span><span class="sxs-lookup"><span data-stu-id="43554-154">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="43554-155">Поддержка именованных параметров с использованием IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="43554-155">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="43554-156">Именованные параметры:</span><span class="sxs-lookup"><span data-stu-id="43554-156">Named options:</span></span>

* <span data-ttu-id="43554-157">полезны, если несколько разделов конфигурации привязываются к одним и тем же свойствам;</span><span class="sxs-lookup"><span data-stu-id="43554-157">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="43554-158">используются с учетом регистра.</span><span class="sxs-lookup"><span data-stu-id="43554-158">Are case sensitive.</span></span>

<span data-ttu-id="43554-159">Рассмотрите следующий файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="43554-159">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="43554-160">Вместо создания двух классов для привязки `TopItem:Month` и `TopItem:Year` для каждого раздела используется следующий класс:</span><span class="sxs-lookup"><span data-stu-id="43554-160">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="43554-161">Следующий код служит для настройки именованных параметров:</span><span class="sxs-lookup"><span data-stu-id="43554-161">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="43554-162">Следующий код отображает именованные параметры:</span><span class="sxs-lookup"><span data-stu-id="43554-162">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="43554-163">Все параметры являются именованными экземплярами.</span><span class="sxs-lookup"><span data-stu-id="43554-163">All options are named instances.</span></span> <span data-ttu-id="43554-164">Экземпляры <xref:Microsoft.Extensions.Options.IConfigureOptions%601> считаются нацеленными на экземпляр `Options.DefaultName`, который имеет значение `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="43554-164"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="43554-165">Интерфейс <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> также реализует интерфейс <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-165"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="43554-166">Реализация <xref:Microsoft.Extensions.Options.IOptionsFactory%601> по умолчанию содержит логику для надлежащего использования каждого экземпляра.</span><span class="sxs-lookup"><span data-stu-id="43554-166">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="43554-167">Именованный параметр `null` предназначен для всех именованных экземпляров, а не для какого-то определенного.</span><span class="sxs-lookup"><span data-stu-id="43554-167">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="43554-168"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> и <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> следуют этому соглашению.</span><span class="sxs-lookup"><span data-stu-id="43554-168"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="43554-169">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="43554-169">OptionsBuilder API</span></span>

<span data-ttu-id="43554-170"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> используется для настройки экземпляров `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="43554-170"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="43554-171">`OptionsBuilder` упрощает создание именованных параметров, так как он является единственным параметром для первоначального вызова `AddOptions<TOptions>(string optionsName)` и не должен появляться во всех последующих вызовах.</span><span class="sxs-lookup"><span data-stu-id="43554-171">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="43554-172">Проверка параметров и перегрузки `ConfigureOptions`, принимающие зависимости службы, доступны только через `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="43554-172">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="43554-173">`OptionsBuilder` используется в разделе [Проверка параметров](#val).</span><span class="sxs-lookup"><span data-stu-id="43554-173">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="43554-174">Использование служб внедрения зависимостей для настройки параметров</span><span class="sxs-lookup"><span data-stu-id="43554-174">Use DI services to configure options</span></span>

<span data-ttu-id="43554-175">Использовать службы, доступные в результате внедрения зависимостей при настройке параметров, можно двумя способами.</span><span class="sxs-lookup"><span data-stu-id="43554-175">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="43554-176">Передайте делегат конфигурации методу [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) в [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="43554-176">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="43554-177">`OptionsBuilder<TOptions>` предоставляет перегрузки метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), которые позволяют использовать до пяти служб для настройки параметров:</span><span class="sxs-lookup"><span data-stu-id="43554-177">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="43554-178">Создайте тип, реализующий <xref:Microsoft.Extensions.Options.IConfigureOptions%601> или <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, и зарегистрируйте этот тип как службу.</span><span class="sxs-lookup"><span data-stu-id="43554-178">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="43554-179">Рекомендуем передать делегат конфигурации методу [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), так как создать службу сложнее.</span><span class="sxs-lookup"><span data-stu-id="43554-179">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="43554-180">Создание типа эквивалентно тому, что делает платформа при вызове метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="43554-180">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="43554-181">При вызове метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) регистрируется временный универсальный интерфейс <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, имеющий конструктор, который принимает указанные универсальные типы службы.</span><span class="sxs-lookup"><span data-stu-id="43554-181">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="43554-182">Проверка параметров</span><span class="sxs-lookup"><span data-stu-id="43554-182">Options validation</span></span>

<span data-ttu-id="43554-183">Проверка параметров позволяет проверять значения параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-183">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="43554-184">Рассмотрите следующий файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="43554-184">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="43554-185">Следующий класс привязывается к разделу конфигурации `"MyConfig"` и применяет несколько правил `DataAnnotations`:</span><span class="sxs-lookup"><span data-stu-id="43554-185">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="43554-186">В приведенном ниже коде</span><span class="sxs-lookup"><span data-stu-id="43554-186">The following code:</span></span>

* <span data-ttu-id="43554-187">вызывает <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A>, чтобы получить класс [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1), который привязывается к классу `MyConfigOptions`;</span><span class="sxs-lookup"><span data-stu-id="43554-187">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="43554-188">вызывает <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> для включения проверки с помощью `DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="43554-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="43554-189">Метод расширения `ValidateDataAnnotations` определен в пакете NuGet [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="43554-189">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="43554-190">Для веб-приложений, использующих пакет SDK `Microsoft.NET.Sdk.Web`, ссылка на этот пакет указывается неявным образом из общей платформы.</span><span class="sxs-lookup"><span data-stu-id="43554-190">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="43554-191">Следующий код отображает значения конфигурации или ошибки проверки:</span><span class="sxs-lookup"><span data-stu-id="43554-191">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="43554-192">Следующий код применяет более сложное правило проверки с использованием делегата:</span><span class="sxs-lookup"><span data-stu-id="43554-192">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="43554-193">IValidateOptions для сложной проверки</span><span class="sxs-lookup"><span data-stu-id="43554-193">IValidateOptions for complex validation</span></span>

<span data-ttu-id="43554-194">Следующий класс реализует <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span><span class="sxs-lookup"><span data-stu-id="43554-194">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="43554-195">`IValidateOptions` позволяет переместить код проверки из `StartUp` в класс.</span><span class="sxs-lookup"><span data-stu-id="43554-195">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="43554-196">С использованием приведенного выше кода проверка включается в `Startup.ConfigureServices` с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="43554-196">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="43554-197">Пост-конфигурация параметров</span><span class="sxs-lookup"><span data-stu-id="43554-197">Options post-configuration</span></span>

<span data-ttu-id="43554-198">Задайте пост-конфигурацию с помощью <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-198">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="43554-199">Пост-конфигурация применяется после выполнения всех действий настройки с помощью <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-199">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="43554-200">Для последующей настройки именованных параметров доступен метод <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>.</span><span class="sxs-lookup"><span data-stu-id="43554-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="43554-201">Для последующей настройки всех экземпляров конфигурации служит метод <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="43554-201">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="43554-202">Доступ к параметрам во время запуска</span><span class="sxs-lookup"><span data-stu-id="43554-202">Accessing options during startup</span></span>

<span data-ttu-id="43554-203"><xref:Microsoft.Extensions.Options.IOptions%601> и <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> можно использовать в методе `Startup.Configure`, так как службы создаются до выполнения метода `Configure`.</span><span class="sxs-lookup"><span data-stu-id="43554-203"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="43554-204">Не используйте <xref:Microsoft.Extensions.Options.IOptions%601> или <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="43554-204">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="43554-205">Из-за очередности регистрации служб состояние параметров может быть несогласованным.</span><span class="sxs-lookup"><span data-stu-id="43554-205">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="43554-206">Пакет NuGet Options.ConfigurationExtensions</span><span class="sxs-lookup"><span data-stu-id="43554-206">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="43554-207">Пакет [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) неявно упоминается в приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43554-207">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="43554-208">Шаблон параметров использует классы для представления групп связанных настроек.</span><span class="sxs-lookup"><span data-stu-id="43554-208">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="43554-209">Когда [параметры конфигурации](xref:fundamentals/configuration/index) изолируются по сценарию в отдельных классах, в приложениях соблюдаются два важных принципа программной инженерии.</span><span class="sxs-lookup"><span data-stu-id="43554-209">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="43554-210">[Принцип разделения интерфейса (ISP) или инкапсуляция.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) Сценарии (классы), которые зависят от параметров конфигурации, зависят только от используемых ими параметров конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-210">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="43554-211">[Разделение областей ответственности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). Параметры для разных частей приложения не зависят друг от друга и не связаны друг с другом.</span><span class="sxs-lookup"><span data-stu-id="43554-211">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="43554-212">В параметрах также предусмотрен механизм для проверки данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-212">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="43554-213">Дополнительные сведения см. в разделе [Проверка параметров](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="43554-213">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="43554-214">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43554-214">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="43554-215">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="43554-215">Prerequisites</span></span>

<span data-ttu-id="43554-216">Добавьте ссылку на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) или добавьте ссылку на пакет в пакет [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="43554-216">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="43554-217">Интерфейсы параметров</span><span class="sxs-lookup"><span data-stu-id="43554-217">Options interfaces</span></span>

<span data-ttu-id="43554-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> используется для извлечения параметров и управления уведомлениями о параметрах для экземпляров `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="43554-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="43554-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> поддерживается в следующих сценариях:</span><span class="sxs-lookup"><span data-stu-id="43554-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="43554-220">уведомления об изменениях;</span><span class="sxs-lookup"><span data-stu-id="43554-220">Change notifications</span></span>
* <span data-ttu-id="43554-221">[именованные параметры](#named-options-support-with-iconfigurenamedoptions);</span><span class="sxs-lookup"><span data-stu-id="43554-221">[Named options](#named-options-support-with-iconfigurenamedoptions)</span></span>
* <span data-ttu-id="43554-222">[перезагружаемые конфигурации](#reload-configuration-data-with-ioptionssnapshot);</span><span class="sxs-lookup"><span data-stu-id="43554-222">[Reloadable configuration](#reload-configuration-data-with-ioptionssnapshot)</span></span>
* <span data-ttu-id="43554-223">объявление определенных параметров недействительными (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>).</span><span class="sxs-lookup"><span data-stu-id="43554-223">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="43554-224">Сценарии [пост-конфигурации](#options-post-configuration) позволяют задать или изменить параметры после выполнения всех действий настройки с помощью <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-224">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="43554-225">Интерфейс <xref:Microsoft.Extensions.Options.IOptionsFactory%601> отвечает за создание экземпляров параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="43554-226">Он имеет единственный метод <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="43554-226">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="43554-227">При реализации по умолчанию принимаются все зарегистрированные интерфейсы <xref:Microsoft.Extensions.Options.IConfigureOptions%601> и <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. Также сначала выполняются все основные настройки, а затем действия после конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-227">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="43554-228">Она различает интерфейсы <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> и <xref:Microsoft.Extensions.Options.IConfigureOptions%601> и вызывает только соответствующий интерфейс.</span><span class="sxs-lookup"><span data-stu-id="43554-228">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="43554-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> используется интерфейсом <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> для записи экземпляров `TOptions` в кэш.</span><span class="sxs-lookup"><span data-stu-id="43554-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="43554-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> делает экземпляры параметров в мониторе недействительными, что приводит к повторному вычислению значений (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="43554-230">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="43554-231">Значения можно также вводить вручную с помощью <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="43554-231">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="43554-232">Метод <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> используется, если необходимо повторно создать все именованные экземпляры по требованию.</span><span class="sxs-lookup"><span data-stu-id="43554-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="43554-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> полезно использовать в сценариях, когда параметры должны заново вычисляться при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="43554-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="43554-234">Дополнительные сведения см. в разделе [Повторная загрузка данных конфигурации с помощью IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="43554-234">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="43554-235"><xref:Microsoft.Extensions.Options.IOptions%601> можно использовать, чтобы обеспечить поддержку определенных параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-235"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="43554-236">Но <xref:Microsoft.Extensions.Options.IOptions%601> не поддерживается в описанных выше сценариях <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-236">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="43554-237">Вы можете продолжать использовать <xref:Microsoft.Extensions.Options.IOptions%601> в существующих платформах и библиотеках, которые уже используют интерфейс <xref:Microsoft.Extensions.Options.IOptions%601>. В таком случае вам не потребуются сценарии, предоставляемые <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-237">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="43554-238">Конфигурация общих параметров</span><span class="sxs-lookup"><span data-stu-id="43554-238">General options configuration</span></span>

<span data-ttu-id="43554-239">Конфигурация общих параметров демонстрируется в примере 1 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="43554-239">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="43554-240">Класс параметров должен быть неабстрактным с открытым конструктором без параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-240">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="43554-241">Приведенный ниже класс (`MyOptions`) имеет два свойства: `Option1` и `Option2`.</span><span class="sxs-lookup"><span data-stu-id="43554-241">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="43554-242">Задавать значения по умолчанию необязательно, однако конструктор класса в этом примере задает значение по умолчанию для `Option1`.</span><span class="sxs-lookup"><span data-stu-id="43554-242">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="43554-243">Значение по умолчанию для `Option2` устанавливается путем прямой инициализации (*Models/MyOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="43554-243">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="43554-244">Класс `MyOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> и привязывается к конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-244">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="43554-245">В следующей страничной модели применяется [внедрение зависимостей конструктора](xref:mvc/controllers/dependency-injection) с помощью <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> для доступа к параметрам (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="43554-245">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="43554-246">В файле *appsettings.json* образца задаются значения для свойств `option1` и `option2`:</span><span class="sxs-lookup"><span data-stu-id="43554-246">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="43554-247">Когда приложение выполняется, метод `OnGet` страничной модели возвращает строку со значениями класса параметров:</span><span class="sxs-lookup"><span data-stu-id="43554-247">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="43554-248">При использовании настраиваемого компонента <xref:System.Configuration.ConfigurationBuilder> для загрузки конфигурации параметров из файла настроек необходимо убедиться, что базовый путь задан правильно:</span><span class="sxs-lookup"><span data-stu-id="43554-248">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="43554-249">При загрузке конфигурации параметров из файла настроек с помощью <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> явно задавать базовый путь не требуется.</span><span class="sxs-lookup"><span data-stu-id="43554-249">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="43554-250">Настройка простых параметров с помощью делегата</span><span class="sxs-lookup"><span data-stu-id="43554-250">Configure simple options with a delegate</span></span>

<span data-ttu-id="43554-251">Настройка простых параметров с помощью делегата демонстрируется в примере 2 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="43554-251">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="43554-252">Используйте делегат для задания значений параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-252">Use a delegate to set options values.</span></span> <span data-ttu-id="43554-253">В образце приложения используется класс `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="43554-253">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="43554-254">В приведенном ниже коде в контейнер службы добавляется еще одна служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-254">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="43554-255">Она использует делегат для настройки привязки к `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="43554-255">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="43554-256">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="43554-256">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="43554-257">Можно добавить несколько поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-257">You can add multiple configuration providers.</span></span> <span data-ttu-id="43554-258">Поставщики конфигурации доступны в пакетах NuGet и применяются в порядке регистрации.</span><span class="sxs-lookup"><span data-stu-id="43554-258">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="43554-259">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="43554-259">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="43554-260">При каждом вызове <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601> добавляется в контейнер службы.</span><span class="sxs-lookup"><span data-stu-id="43554-260">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="43554-261">В предыдущем примере значения `Option1` и `Option2` задаются в файле *appsettings.json*, однако значения `Option1` и `Option2` переопределяются настроенным делегатом.</span><span class="sxs-lookup"><span data-stu-id="43554-261">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="43554-262">Если включено более одной службы конфигурации, последний источник конфигурации *имеет приоритет* и определяет значение конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-262">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="43554-263">Когда приложение выполняется, метод `OnGet` страничной модели возвращает строку со значениями класса параметров:</span><span class="sxs-lookup"><span data-stu-id="43554-263">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="43554-264">Конфигурация подпараметров</span><span class="sxs-lookup"><span data-stu-id="43554-264">Suboptions configuration</span></span>

<span data-ttu-id="43554-265">Конфигурация подпараметров демонстрируется в примере 3 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="43554-265">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="43554-266">В приложениях должны создаваться классы приложений, относящиеся к определенным группам сценариев (классам).</span><span class="sxs-lookup"><span data-stu-id="43554-266">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="43554-267">Части приложения, которым требуются значения конфигурации, должны иметь доступ только к используемым ими значениям конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-267">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="43554-268">При привязке параметров к конфигурации каждое свойство, относящееся к типу параметров, привязывается к ключу конфигурации в формате `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="43554-268">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="43554-269">Например, свойство `MyOptions.Option1` привязывается к ключу `Option1`, который считывается из свойства `option1` в файле *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="43554-269">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="43554-270">В приведенном ниже коде в контейнер службы добавляется третья служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-270">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="43554-271">Она привязывает `MySubOptions` к разделу `subsection` файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="43554-271">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="43554-272">Методу `GetSection` нужно пространство имен <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="43554-272">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="43554-273">В файле *appsettings.json* образца определяется член `subsection` с ключами для `suboption1` и `suboption2`.</span><span class="sxs-lookup"><span data-stu-id="43554-273">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="43554-274">Класс `MySubOptions` определяет свойства `SubOption1` и `SubOption2` для хранения значений параметров (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="43554-274">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="43554-275">Метод `OnGet` страничной модели возвращает строку со значениями параметров (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="43554-275">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="43554-276">Когда приложение выполняется, метод `OnGet` возвращает строку со значениями класса подпараметров:</span><span class="sxs-lookup"><span data-stu-id="43554-276">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="43554-277">Внедрение параметров</span><span class="sxs-lookup"><span data-stu-id="43554-277">Options injection</span></span>

<span data-ttu-id="43554-278">Внедрение параметров демонстрируется в примере 4 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="43554-278">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="43554-279">Внедрите <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> в:</span><span class="sxs-lookup"><span data-stu-id="43554-279">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="43554-280">Страница Razor или представление MVC с директивой [`@inject`](xref:mvc/views/razor#inject) Razor.</span><span class="sxs-lookup"><span data-stu-id="43554-280">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="43554-281">Модель страницы или представления.</span><span class="sxs-lookup"><span data-stu-id="43554-281">A page or view model.</span></span>

<span data-ttu-id="43554-282">Следующий пример из примера приложения внедряет <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> в модель страницы (*Pages/index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="43554-282">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="43554-283">В примере приложения показано, как внедрить `IOptionsMonitor<MyOptions>` с директивой `@inject`.</span><span class="sxs-lookup"><span data-stu-id="43554-283">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="43554-284">Когда запускается приложение, значения параметров появляются на отображаемой странице:</span><span class="sxs-lookup"><span data-stu-id="43554-284">When the app is run, the options values are shown in the rendered page:</span></span>

![Значения параметров Option1: value1_from_json и Option2: –1 загружаются из модели и путем внедрения в представление.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="43554-286">Повторная загрузка данных конфигурации с помощью IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="43554-286">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="43554-287">Повторная загрузка данных конфигурации с помощью <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> демонстрируется в примере 5 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="43554-287">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="43554-288">С помощью <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> параметры вычисляются один раз на каждый запрос при обращении к ним и кэшируются на все время существования запроса.</span><span class="sxs-lookup"><span data-stu-id="43554-288">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="43554-289">Разница между `IOptionsMonitor` и `IOptionsSnapshot`:</span><span class="sxs-lookup"><span data-stu-id="43554-289">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="43554-290">`IOptionsMonitor` — это [одноэлементная служба](xref:fundamentals/dependency-injection#singleton), которая получает текущие значения параметров в любое время, что особенно полезно в одноэлементных зависимостях.</span><span class="sxs-lookup"><span data-stu-id="43554-290">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="43554-291">`IOptionsSnapshot` — это [служба с заданной областью действия](xref:fundamentals/dependency-injection#scoped), предоставляющая моментальный снимок параметров на момент создания объекта `IOptionsSnapshot<T>`.</span><span class="sxs-lookup"><span data-stu-id="43554-291">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="43554-292">Моментальные снимки параметров предназначены для использования с временными зависимостями и зависимостями с заданной областью действия.</span><span class="sxs-lookup"><span data-stu-id="43554-292">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="43554-293">В приведенном ниже примере демонстрируется создание экземпляра <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> после изменения файла *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="43554-293">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="43554-294">Несколько запросов к серверу возвращают константные значения, предоставляемые файлом *appsettings.json*, пока файл не изменится и конфигурация не загрузится повторно.</span><span class="sxs-lookup"><span data-stu-id="43554-294">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="43554-295">Ниже показаны начальные значения `option1` и `option2`, загруженные из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="43554-295">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="43554-296">Измените значения в файле *appsettings.json* на `value1_from_json UPDATED` и `200`.</span><span class="sxs-lookup"><span data-stu-id="43554-296">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="43554-297">Сохраните файл *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="43554-297">Save the *appsettings.json* file.</span></span> <span data-ttu-id="43554-298">Обновите окно браузера, чтобы увидеть обновленные значения параметров:</span><span class="sxs-lookup"><span data-stu-id="43554-298">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="43554-299">Поддержка именованных параметров в IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="43554-299">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="43554-300">Включение поддержки именованных параметров в <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> демонстрируется в примере 6 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="43554-300">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="43554-301">Поддержка именованных параметров позволяет приложению различать конфигурации именованных параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-301">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="43554-302">В примере приложения именованные параметры должны быть объявлены с помощью элемента [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), который вызывает метод расширения [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="43554-302">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="43554-303">Именованные параметры вводятся с учетом регистра.</span><span class="sxs-lookup"><span data-stu-id="43554-303">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="43554-304">Пример приложения обращается к именованным параметрам с помощью метода <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="43554-304">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="43554-305">При запуске образца приложения возвращаются именованные параметры:</span><span class="sxs-lookup"><span data-stu-id="43554-305">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="43554-306">Значения `named_options_1` предоставляются из конфигурации, которая загружается из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="43554-306">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="43554-307">Значения `named_options_2` предоставляются следующим образом:</span><span class="sxs-lookup"><span data-stu-id="43554-307">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="43554-308">Делегатом `named_options_2` в `ConfigureServices` для `Option1`.</span><span class="sxs-lookup"><span data-stu-id="43554-308">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="43554-309">Значение по умолчанию для `Option2` предоставляется классом `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="43554-309">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="43554-310">Настройка всех параметров с помощью метода ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="43554-310">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="43554-311">Настройте все экземпляры параметров с помощью метода <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="43554-311">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="43554-312">Приведенный ниже код настраивает `Option1` для всех экземпляров конфигурации с общим значением.</span><span class="sxs-lookup"><span data-stu-id="43554-312">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="43554-313">Добавьте следующий код в метод `Startup.ConfigureServices` вручную:</span><span class="sxs-lookup"><span data-stu-id="43554-313">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="43554-314">Если запустить образец приложения после добавления, результат будет следующим:</span><span class="sxs-lookup"><span data-stu-id="43554-314">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="43554-315">Все параметры являются именованными экземплярами.</span><span class="sxs-lookup"><span data-stu-id="43554-315">All options are named instances.</span></span> <span data-ttu-id="43554-316">Существующие экземпляры <xref:Microsoft.Extensions.Options.IConfigureOptions%601> считаются нацеленными на экземпляр `Options.DefaultName`, который имеет значение `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="43554-316">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="43554-317">Интерфейс <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> также реализует интерфейс <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="43554-318">Реализация <xref:Microsoft.Extensions.Options.IOptionsFactory%601> по умолчанию содержит логику для надлежащего использования каждого экземпляра.</span><span class="sxs-lookup"><span data-stu-id="43554-318">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="43554-319">Именованный параметр `null` предназначен для всех именованных экземпляров, а не для какого-то определенного (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> и <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> следуют этому соглашению).</span><span class="sxs-lookup"><span data-stu-id="43554-319">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="43554-320">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="43554-320">OptionsBuilder API</span></span>

<span data-ttu-id="43554-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> используется для настройки экземпляров `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="43554-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="43554-322">`OptionsBuilder` упрощает создание именованных параметров, так как он является единственным параметром для первоначального вызова `AddOptions<TOptions>(string optionsName)` и не должен появляться во всех последующих вызовах.</span><span class="sxs-lookup"><span data-stu-id="43554-322">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="43554-323">Проверка параметров и перегрузки `ConfigureOptions`, принимающие зависимости службы, доступны только через `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="43554-323">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="43554-324">Использование служб внедрения зависимостей для настройки параметров</span><span class="sxs-lookup"><span data-stu-id="43554-324">Use DI services to configure options</span></span>

<span data-ttu-id="43554-325">Вы можете получить доступ к другим службам, доступным в результате внедрения зависимостей при настройке параметров, двумя способами.</span><span class="sxs-lookup"><span data-stu-id="43554-325">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="43554-326">Передайте делегат конфигурации методу [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) в [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="43554-326">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="43554-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) предоставляет перегрузки метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), которые позволяют использовать до пяти служб для настройки параметров:</span><span class="sxs-lookup"><span data-stu-id="43554-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="43554-328">Создайте собственный тип, реализующий <xref:Microsoft.Extensions.Options.IConfigureOptions%601> или <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, и зарегистрируйте этот тип как службу.</span><span class="sxs-lookup"><span data-stu-id="43554-328">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="43554-329">Рекомендуем передать делегат конфигурации методу [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), так как создать службу сложнее.</span><span class="sxs-lookup"><span data-stu-id="43554-329">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="43554-330">Создание собственного типа эквивалентно тому, что делает платформа при использовании метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="43554-330">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="43554-331">При вызове метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) регистрируется временный универсальный интерфейс <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, имеющий конструктор, который принимает указанные универсальные типы службы.</span><span class="sxs-lookup"><span data-stu-id="43554-331">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="43554-332">Проверка параметров</span><span class="sxs-lookup"><span data-stu-id="43554-332">Options validation</span></span>

<span data-ttu-id="43554-333">Эта функция позволяет проверить параметры при их настройке.</span><span class="sxs-lookup"><span data-stu-id="43554-333">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="43554-334">Вызовите `Validate` с использованием метода проверки, который возвращает `true`, если параметры являются допустимыми, и `false`, если они являются недопустимыми:</span><span class="sxs-lookup"><span data-stu-id="43554-334">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="43554-335">В предыдущем примере для именованного экземпляра параметров задается `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="43554-335">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="43554-336">Экземпляр параметров по умолчанию — `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="43554-336">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="43554-337">Проверка выполняется при создании экземпляра параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-337">Validation runs when the options instance is created.</span></span> <span data-ttu-id="43554-338">Экземпляр параметров гарантированно проходит проверку при первом обращении.</span><span class="sxs-lookup"><span data-stu-id="43554-338">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="43554-339">Проверка параметров не защищает от изменений, вносимых после создания экземпляра параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-339">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="43554-340">Например, параметры `IOptionsSnapshot` создаются и проверяются один раз для каждого запроса при первом обращении к параметрам.</span><span class="sxs-lookup"><span data-stu-id="43554-340">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="43554-341">Параметры `IOptionsSnapshot` не проверяются повторно при последующих попытках доступа *для этого же запроса*.</span><span class="sxs-lookup"><span data-stu-id="43554-341">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="43554-342">Метод `Validate` принимает `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="43554-342">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="43554-343">Чтобы полностью настроить проверку, реализуйте `IValidateOptions<TOptions>`, чтобы:</span><span class="sxs-lookup"><span data-stu-id="43554-343">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="43554-344">выполнить проверку нескольких типов параметров — `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`;</span><span class="sxs-lookup"><span data-stu-id="43554-344">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="43554-345">выполнить проверку, зависящую от другого типа параметра — `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`.</span><span class="sxs-lookup"><span data-stu-id="43554-345">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="43554-346">`IValidateOptions` проверяет:</span><span class="sxs-lookup"><span data-stu-id="43554-346">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="43554-347">определенный именованный экземпляр параметров;</span><span class="sxs-lookup"><span data-stu-id="43554-347">A specific named options instance.</span></span>
* <span data-ttu-id="43554-348">все параметры, при которых `name` имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="43554-348">All options when `name` is `null`.</span></span>

<span data-ttu-id="43554-349">Возвращает `ValidateOptionsResult` из реализации интерфейса:</span><span class="sxs-lookup"><span data-stu-id="43554-349">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="43554-350">Проверка на основе заметок к данным доступна в пакете [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) с помощью вызова метода <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> в `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="43554-350">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="43554-351">`Microsoft.Extensions.Options.DataAnnotations` входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="43554-351">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="43554-352">Безотложная проверка (быстрое прекращение при запуске) находится на этапе рассмотрения для включения в будущие выпуски.</span><span class="sxs-lookup"><span data-stu-id="43554-352">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="43554-353">Пост-конфигурация параметров</span><span class="sxs-lookup"><span data-stu-id="43554-353">Options post-configuration</span></span>

<span data-ttu-id="43554-354">Задайте пост-конфигурацию с помощью <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-354">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="43554-355">Пост-конфигурация применяется после выполнения всех действий настройки с помощью <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-355">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="43554-356">Для последующей настройки именованных параметров доступен метод <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>.</span><span class="sxs-lookup"><span data-stu-id="43554-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="43554-357">Для последующей настройки всех экземпляров конфигурации служит метод <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="43554-357">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="43554-358">Доступ к параметрам во время запуска</span><span class="sxs-lookup"><span data-stu-id="43554-358">Accessing options during startup</span></span>

<span data-ttu-id="43554-359"><xref:Microsoft.Extensions.Options.IOptions%601> и <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> можно использовать в методе `Startup.Configure`, так как службы создаются до выполнения метода `Configure`.</span><span class="sxs-lookup"><span data-stu-id="43554-359"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="43554-360">Не используйте <xref:Microsoft.Extensions.Options.IOptions%601> или <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="43554-360">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="43554-361">Из-за очередности регистрации служб состояние параметров может быть несогласованным.</span><span class="sxs-lookup"><span data-stu-id="43554-361">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="43554-362">Шаблон параметров использует классы для представления групп связанных настроек.</span><span class="sxs-lookup"><span data-stu-id="43554-362">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="43554-363">Когда [параметры конфигурации](xref:fundamentals/configuration/index) изолируются по сценарию в отдельных классах, в приложениях соблюдаются два важных принципа программной инженерии.</span><span class="sxs-lookup"><span data-stu-id="43554-363">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="43554-364">[Принцип разделения интерфейса (ISP) или инкапсуляция.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) Сценарии (классы), которые зависят от параметров конфигурации, зависят только от используемых ими параметров конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-364">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="43554-365">[Разделение областей ответственности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). Параметры для разных частей приложения не зависят друг от друга и не связаны друг с другом.</span><span class="sxs-lookup"><span data-stu-id="43554-365">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="43554-366">В параметрах также предусмотрен механизм для проверки данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-366">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="43554-367">Дополнительные сведения см. в разделе [Проверка параметров](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="43554-367">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="43554-368">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43554-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="43554-369">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="43554-369">Prerequisites</span></span>

<span data-ttu-id="43554-370">Добавьте ссылку на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) или добавьте ссылку на пакет в пакет [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="43554-370">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="43554-371">Интерфейсы параметров</span><span class="sxs-lookup"><span data-stu-id="43554-371">Options interfaces</span></span>

<span data-ttu-id="43554-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> используется для извлечения параметров и управления уведомлениями о параметрах для экземпляров `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="43554-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="43554-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> поддерживается в следующих сценариях:</span><span class="sxs-lookup"><span data-stu-id="43554-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="43554-374">уведомления об изменениях;</span><span class="sxs-lookup"><span data-stu-id="43554-374">Change notifications</span></span>
* <span data-ttu-id="43554-375">[именованные параметры](#named-options-support-with-iconfigurenamedoptions);</span><span class="sxs-lookup"><span data-stu-id="43554-375">[Named options](#named-options-support-with-iconfigurenamedoptions)</span></span>
* <span data-ttu-id="43554-376">[перезагружаемые конфигурации](#reload-configuration-data-with-ioptionssnapshot);</span><span class="sxs-lookup"><span data-stu-id="43554-376">[Reloadable configuration](#reload-configuration-data-with-ioptionssnapshot)</span></span>
* <span data-ttu-id="43554-377">объявление определенных параметров недействительными (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>).</span><span class="sxs-lookup"><span data-stu-id="43554-377">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="43554-378">Сценарии [пост-конфигурации](#options-post-configuration) позволяют задать или изменить параметры после выполнения всех действий настройки с помощью <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-378">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="43554-379">Интерфейс <xref:Microsoft.Extensions.Options.IOptionsFactory%601> отвечает за создание экземпляров параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="43554-380">Он имеет единственный метод <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="43554-380">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="43554-381">При реализации по умолчанию принимаются все зарегистрированные интерфейсы <xref:Microsoft.Extensions.Options.IConfigureOptions%601> и <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. Также сначала выполняются все основные настройки, а затем действия после конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-381">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="43554-382">Она различает интерфейсы <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> и <xref:Microsoft.Extensions.Options.IConfigureOptions%601> и вызывает только соответствующий интерфейс.</span><span class="sxs-lookup"><span data-stu-id="43554-382">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="43554-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> используется интерфейсом <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> для записи экземпляров `TOptions` в кэш.</span><span class="sxs-lookup"><span data-stu-id="43554-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="43554-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> делает экземпляры параметров в мониторе недействительными, что приводит к повторному вычислению значений (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="43554-384">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="43554-385">Значения можно также вводить вручную с помощью <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="43554-385">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="43554-386">Метод <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> используется, если необходимо повторно создать все именованные экземпляры по требованию.</span><span class="sxs-lookup"><span data-stu-id="43554-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="43554-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> полезно использовать в сценариях, когда параметры должны заново вычисляться при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="43554-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="43554-388">Дополнительные сведения см. в разделе [Повторная загрузка данных конфигурации с помощью IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="43554-388">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="43554-389"><xref:Microsoft.Extensions.Options.IOptions%601> можно использовать, чтобы обеспечить поддержку определенных параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-389"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="43554-390">Но <xref:Microsoft.Extensions.Options.IOptions%601> не поддерживается в описанных выше сценариях <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-390">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="43554-391">Вы можете продолжать использовать <xref:Microsoft.Extensions.Options.IOptions%601> в существующих платформах и библиотеках, которые уже используют интерфейс <xref:Microsoft.Extensions.Options.IOptions%601>. В таком случае вам не потребуются сценарии, предоставляемые <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-391">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="43554-392">Конфигурация общих параметров</span><span class="sxs-lookup"><span data-stu-id="43554-392">General options configuration</span></span>

<span data-ttu-id="43554-393">Конфигурация общих параметров демонстрируется в примере 1 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="43554-393">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="43554-394">Класс параметров должен быть неабстрактным с открытым конструктором без параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-394">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="43554-395">Приведенный ниже класс (`MyOptions`) имеет два свойства: `Option1` и `Option2`.</span><span class="sxs-lookup"><span data-stu-id="43554-395">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="43554-396">Задавать значения по умолчанию необязательно, однако конструктор класса в этом примере задает значение по умолчанию для `Option1`.</span><span class="sxs-lookup"><span data-stu-id="43554-396">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="43554-397">Значение по умолчанию для `Option2` устанавливается путем прямой инициализации (*Models/MyOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="43554-397">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="43554-398">Класс `MyOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> и привязывается к конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-398">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="43554-399">В следующей страничной модели применяется [внедрение зависимостей конструктора](xref:mvc/controllers/dependency-injection) с помощью <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> для доступа к параметрам (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="43554-399">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="43554-400">В файле *appsettings.json* образца задаются значения для свойств `option1` и `option2`:</span><span class="sxs-lookup"><span data-stu-id="43554-400">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="43554-401">Когда приложение выполняется, метод `OnGet` страничной модели возвращает строку со значениями класса параметров:</span><span class="sxs-lookup"><span data-stu-id="43554-401">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="43554-402">При использовании настраиваемого компонента <xref:System.Configuration.ConfigurationBuilder> для загрузки конфигурации параметров из файла настроек необходимо убедиться, что базовый путь задан правильно:</span><span class="sxs-lookup"><span data-stu-id="43554-402">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="43554-403">При загрузке конфигурации параметров из файла настроек с помощью <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> явно задавать базовый путь не требуется.</span><span class="sxs-lookup"><span data-stu-id="43554-403">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="43554-404">Настройка простых параметров с помощью делегата</span><span class="sxs-lookup"><span data-stu-id="43554-404">Configure simple options with a delegate</span></span>

<span data-ttu-id="43554-405">Настройка простых параметров с помощью делегата демонстрируется в примере 2 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="43554-405">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="43554-406">Используйте делегат для задания значений параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-406">Use a delegate to set options values.</span></span> <span data-ttu-id="43554-407">В образце приложения используется класс `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="43554-407">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="43554-408">В приведенном ниже коде в контейнер службы добавляется еще одна служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-408">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="43554-409">Она использует делегат для настройки привязки к `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="43554-409">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="43554-410">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="43554-410">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="43554-411">Можно добавить несколько поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-411">You can add multiple configuration providers.</span></span> <span data-ttu-id="43554-412">Поставщики конфигурации доступны в пакетах NuGet и применяются в порядке регистрации.</span><span class="sxs-lookup"><span data-stu-id="43554-412">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="43554-413">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="43554-413">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="43554-414">При каждом вызове <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601> добавляется в контейнер службы.</span><span class="sxs-lookup"><span data-stu-id="43554-414">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="43554-415">В предыдущем примере значения `Option1` и `Option2` задаются в файле *appsettings.json*, однако значения `Option1` и `Option2` переопределяются настроенным делегатом.</span><span class="sxs-lookup"><span data-stu-id="43554-415">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="43554-416">Если включено более одной службы конфигурации, последний источник конфигурации *имеет приоритет* и определяет значение конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-416">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="43554-417">Когда приложение выполняется, метод `OnGet` страничной модели возвращает строку со значениями класса параметров:</span><span class="sxs-lookup"><span data-stu-id="43554-417">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="43554-418">Конфигурация подпараметров</span><span class="sxs-lookup"><span data-stu-id="43554-418">Suboptions configuration</span></span>

<span data-ttu-id="43554-419">Конфигурация подпараметров демонстрируется в примере 3 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="43554-419">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="43554-420">В приложениях должны создаваться классы приложений, относящиеся к определенным группам сценариев (классам).</span><span class="sxs-lookup"><span data-stu-id="43554-420">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="43554-421">Части приложения, которым требуются значения конфигурации, должны иметь доступ только к используемым ими значениям конфигурации.</span><span class="sxs-lookup"><span data-stu-id="43554-421">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="43554-422">При привязке параметров к конфигурации каждое свойство, относящееся к типу параметров, привязывается к ключу конфигурации в формате `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="43554-422">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="43554-423">Например, свойство `MyOptions.Option1` привязывается к ключу `Option1`, который считывается из свойства `option1` в файле *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="43554-423">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="43554-424">В приведенном ниже коде в контейнер службы добавляется третья служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-424">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="43554-425">Она привязывает `MySubOptions` к разделу `subsection` файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="43554-425">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="43554-426">Методу `GetSection` нужно пространство имен <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="43554-426">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="43554-427">В файле *appsettings.json* образца определяется член `subsection` с ключами для `suboption1` и `suboption2`.</span><span class="sxs-lookup"><span data-stu-id="43554-427">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="43554-428">Класс `MySubOptions` определяет свойства `SubOption1` и `SubOption2` для хранения значений параметров (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="43554-428">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="43554-429">Метод `OnGet` страничной модели возвращает строку со значениями параметров (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="43554-429">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="43554-430">Когда приложение выполняется, метод `OnGet` возвращает строку со значениями класса подпараметров:</span><span class="sxs-lookup"><span data-stu-id="43554-430">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="43554-431">Параметры, предоставляемые моделью представления или посредством прямого внедрения представления</span><span class="sxs-lookup"><span data-stu-id="43554-431">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="43554-432">Параметры, предоставляемые моделью представления или посредством прямого внедрения представления, демонстрируются в примере 4 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="43554-432">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="43554-433">Параметры могут передаваться в модель представления или путем внедрения <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> непосредственно в представление (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="43554-433">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="43554-434">В примере приложения показано, как внедрить `IOptionsMonitor<MyOptions>` с директивой `@inject`.</span><span class="sxs-lookup"><span data-stu-id="43554-434">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="43554-435">Когда запускается приложение, значения параметров появляются на отображаемой странице:</span><span class="sxs-lookup"><span data-stu-id="43554-435">When the app is run, the options values are shown in the rendered page:</span></span>

![Значения параметров Option1: value1_from_json и Option2: –1 загружаются из модели и путем внедрения в представление.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="43554-437">Повторная загрузка данных конфигурации с помощью IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="43554-437">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="43554-438">Повторная загрузка данных конфигурации с помощью <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> демонстрируется в примере 5 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="43554-438">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="43554-439">Интерфейс <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> поддерживает повторную загрузку параметров с минимальными затратами на обработку.</span><span class="sxs-lookup"><span data-stu-id="43554-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="43554-440">Параметры вычисляются один раз на каждый запрос при обращении к ним и кэшируются на все время существования запроса.</span><span class="sxs-lookup"><span data-stu-id="43554-440">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="43554-441">В приведенном ниже примере демонстрируется создание экземпляра <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> после изменения файла *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="43554-441">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="43554-442">Несколько запросов к серверу возвращают константные значения, предоставляемые файлом *appsettings.json*, пока файл не изменится и конфигурация не загрузится повторно.</span><span class="sxs-lookup"><span data-stu-id="43554-442">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="43554-443">Ниже показаны начальные значения `option1` и `option2`, загруженные из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="43554-443">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="43554-444">Измените значения в файле *appsettings.json* на `value1_from_json UPDATED` и `200`.</span><span class="sxs-lookup"><span data-stu-id="43554-444">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="43554-445">Сохраните файл *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="43554-445">Save the *appsettings.json* file.</span></span> <span data-ttu-id="43554-446">Обновите окно браузера, чтобы увидеть обновленные значения параметров:</span><span class="sxs-lookup"><span data-stu-id="43554-446">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="43554-447">Поддержка именованных параметров в IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="43554-447">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="43554-448">Включение поддержки именованных параметров в <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> демонстрируется в примере 6 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="43554-448">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="43554-449">Поддержка именованных параметров позволяет приложению различать конфигурации именованных параметров.</span><span class="sxs-lookup"><span data-stu-id="43554-449">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="43554-450">В примере приложения именованные параметры должны быть объявлены с помощью элемента [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), который вызывает метод расширения [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="43554-450">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="43554-451">Именованные параметры вводятся с учетом регистра.</span><span class="sxs-lookup"><span data-stu-id="43554-451">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="43554-452">Пример приложения обращается к именованным параметрам с помощью метода <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="43554-452">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="43554-453">При запуске образца приложения возвращаются именованные параметры:</span><span class="sxs-lookup"><span data-stu-id="43554-453">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="43554-454">Значения `named_options_1` предоставляются из конфигурации, которая загружается из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="43554-454">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="43554-455">Значения `named_options_2` предоставляются следующим образом:</span><span class="sxs-lookup"><span data-stu-id="43554-455">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="43554-456">Делегатом `named_options_2` в `ConfigureServices` для `Option1`.</span><span class="sxs-lookup"><span data-stu-id="43554-456">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="43554-457">Значение по умолчанию для `Option2` предоставляется классом `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="43554-457">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="43554-458">Настройка всех параметров с помощью метода ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="43554-458">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="43554-459">Настройте все экземпляры параметров с помощью метода <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="43554-459">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="43554-460">Приведенный ниже код настраивает `Option1` для всех экземпляров конфигурации с общим значением.</span><span class="sxs-lookup"><span data-stu-id="43554-460">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="43554-461">Добавьте следующий код в метод `Startup.ConfigureServices` вручную:</span><span class="sxs-lookup"><span data-stu-id="43554-461">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="43554-462">Если запустить образец приложения после добавления, результат будет следующим:</span><span class="sxs-lookup"><span data-stu-id="43554-462">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="43554-463">Все параметры являются именованными экземплярами.</span><span class="sxs-lookup"><span data-stu-id="43554-463">All options are named instances.</span></span> <span data-ttu-id="43554-464">Существующие экземпляры <xref:Microsoft.Extensions.Options.IConfigureOptions%601> считаются нацеленными на экземпляр `Options.DefaultName`, который имеет значение `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="43554-464">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="43554-465">Интерфейс <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> также реализует интерфейс <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="43554-466">Реализация <xref:Microsoft.Extensions.Options.IOptionsFactory%601> по умолчанию содержит логику для надлежащего использования каждого экземпляра.</span><span class="sxs-lookup"><span data-stu-id="43554-466">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="43554-467">Именованный параметр `null` предназначен для всех именованных экземпляров, а не для какого-то определенного (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> и <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> следуют этому соглашению).</span><span class="sxs-lookup"><span data-stu-id="43554-467">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="43554-468">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="43554-468">OptionsBuilder API</span></span>

<span data-ttu-id="43554-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> используется для настройки экземпляров `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="43554-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="43554-470">`OptionsBuilder` упрощает создание именованных параметров, так как он является единственным параметром для первоначального вызова `AddOptions<TOptions>(string optionsName)` и не должен появляться во всех последующих вызовах.</span><span class="sxs-lookup"><span data-stu-id="43554-470">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="43554-471">Проверка параметров и перегрузки `ConfigureOptions`, принимающие зависимости службы, доступны только через `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="43554-471">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="43554-472">Использование служб внедрения зависимостей для настройки параметров</span><span class="sxs-lookup"><span data-stu-id="43554-472">Use DI services to configure options</span></span>

<span data-ttu-id="43554-473">Вы можете получить доступ к другим службам, доступным в результате внедрения зависимостей при настройке параметров, двумя способами.</span><span class="sxs-lookup"><span data-stu-id="43554-473">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="43554-474">Передайте делегат конфигурации методу [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) в [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="43554-474">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="43554-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) предоставляет перегрузки метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), которые позволяют использовать до пяти служб для настройки параметров:</span><span class="sxs-lookup"><span data-stu-id="43554-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="43554-476">Создайте собственный тип, реализующий <xref:Microsoft.Extensions.Options.IConfigureOptions%601> или <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, и зарегистрируйте этот тип как службу.</span><span class="sxs-lookup"><span data-stu-id="43554-476">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="43554-477">Рекомендуем передать делегат конфигурации методу [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), так как создать службу сложнее.</span><span class="sxs-lookup"><span data-stu-id="43554-477">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="43554-478">Создание собственного типа эквивалентно тому, что делает платформа при использовании метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="43554-478">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="43554-479">При вызове метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) регистрируется временный универсальный интерфейс <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, имеющий конструктор, который принимает указанные универсальные типы службы.</span><span class="sxs-lookup"><span data-stu-id="43554-479">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="43554-480">Пост-конфигурация параметров</span><span class="sxs-lookup"><span data-stu-id="43554-480">Options post-configuration</span></span>

<span data-ttu-id="43554-481">Задайте пост-конфигурацию с помощью <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-481">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="43554-482">Пост-конфигурация применяется после выполнения всех действий настройки с помощью <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="43554-482">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="43554-483">Для последующей настройки именованных параметров доступен метод <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>.</span><span class="sxs-lookup"><span data-stu-id="43554-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="43554-484">Для последующей настройки всех экземпляров конфигурации служит метод <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="43554-484">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="43554-485">Доступ к параметрам во время запуска</span><span class="sxs-lookup"><span data-stu-id="43554-485">Accessing options during startup</span></span>

<span data-ttu-id="43554-486"><xref:Microsoft.Extensions.Options.IOptions%601> и <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> можно использовать в методе `Startup.Configure`, так как службы создаются до выполнения метода `Configure`.</span><span class="sxs-lookup"><span data-stu-id="43554-486"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="43554-487">Не используйте <xref:Microsoft.Extensions.Options.IOptions%601> или <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="43554-487">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="43554-488">Из-за очередности регистрации служб состояние параметров может быть несогласованным.</span><span class="sxs-lookup"><span data-stu-id="43554-488">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="43554-489">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="43554-489">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
