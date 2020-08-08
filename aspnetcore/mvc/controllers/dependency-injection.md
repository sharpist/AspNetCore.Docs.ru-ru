---
title: Внедрение зависимостей в контроллеры в ASP.NET Core
author: ardalis
description: Узнайте, как контроллеры MVC ASP.NET Core явным образом запрашивают зависимости с помощью конструкторов с внедрением зависимостей в ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: aabb7f893fd9650e2e901dcfdfe845faba391435
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019176"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="275ed-103">Внедрение зависимостей в контроллеры в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="275ed-103">Dependency injection into controllers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="275ed-104">Авторы: [Shadi Namrouti](https://github.com/shadinamrouti) (Шади Намрути), [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Стив Смит](https://github.com/ardalis) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="275ed-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="275ed-105">Контроллеры ASP.NET Core MVC запрашивают зависимости явно с помощью конструкторов.</span><span class="sxs-lookup"><span data-stu-id="275ed-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="275ed-106">ASP.NET Core имеет встроенную поддержку [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="275ed-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="275ed-107">Внедрение зависимостей упрощает тестирование и поддержку приложений.</span><span class="sxs-lookup"><span data-stu-id="275ed-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="275ed-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="275ed-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="275ed-109">Внедрение через конструктор</span><span class="sxs-lookup"><span data-stu-id="275ed-109">Constructor Injection</span></span>

<span data-ttu-id="275ed-110">Службы добавляются в качестве параметра конструктора, и среда выполнения разрешает службу из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="275ed-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="275ed-111">Службы обычно задаются с помощью интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="275ed-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="275ed-112">Например, рассмотрим приложение, которому требуется текущее время.</span><span class="sxs-lookup"><span data-stu-id="275ed-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="275ed-113">Следующие интерфейсы предоставляют службу `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="275ed-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="275ed-114">В следующем коде реализуется интерфейс `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="275ed-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="275ed-115">Добавьте службу в контейнер службы:</span><span class="sxs-lookup"><span data-stu-id="275ed-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="275ed-116">Дополнительные сведения о <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, см. в разделе [Время существования службы внедрения зависимости](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="275ed-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="275ed-117">Следующий код отображает приветствие пользователю в зависимости от времени дня:</span><span class="sxs-lookup"><span data-stu-id="275ed-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="275ed-118">Запустите приложение, и в зависимости от времени отобразится сообщение.</span><span class="sxs-lookup"><span data-stu-id="275ed-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="275ed-119">Внедрение действий с помощью FromServices</span><span class="sxs-lookup"><span data-stu-id="275ed-119">Action injection with FromServices</span></span>

<span data-ttu-id="275ed-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> позволяет внедрять службу непосредственно в метод действия без использования внедрения через конструктор:</span><span class="sxs-lookup"><span data-stu-id="275ed-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="275ed-121">Доступ к параметрам из контроллера</span><span class="sxs-lookup"><span data-stu-id="275ed-121">Access settings from a controller</span></span>

<span data-ttu-id="275ed-122">Доступ к параметрам приложения или конфигурации из контроллера относится к типичным шаблонам.</span><span class="sxs-lookup"><span data-stu-id="275ed-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="275ed-123">*Шаблон параметров*, описанный в разделе <xref:fundamentals/configuration/options>, является предпочтительным подходом для управления параметрами.</span><span class="sxs-lookup"><span data-stu-id="275ed-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="275ed-124">Как правило, не следует напрямую внедрять <xref:Microsoft.Extensions.Configuration.IConfiguration> в контроллер.</span><span class="sxs-lookup"><span data-stu-id="275ed-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="275ed-125">Создайте класс, представляющий параметры.</span><span class="sxs-lookup"><span data-stu-id="275ed-125">Create a class that represents the options.</span></span> <span data-ttu-id="275ed-126">Например:</span><span class="sxs-lookup"><span data-stu-id="275ed-126">For example:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="275ed-127">Добавьте класс конфигурации в коллекцию служб:</span><span class="sxs-lookup"><span data-stu-id="275ed-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="275ed-128">Настройте приложение для чтения параметров из файла формата JSON:</span><span class="sxs-lookup"><span data-stu-id="275ed-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="275ed-129">Следующий код запрашивает параметры `IOptions<SampleWebSettings>` из контейнера служб и использует их в методе `Index`:</span><span class="sxs-lookup"><span data-stu-id="275ed-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="275ed-130">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="275ed-130">Additional resources</span></span>

* <span data-ttu-id="275ed-131">См. раздел <xref:mvc/controllers/testing>, чтобы узнать, как упростить тестирование кода, явно запросив зависимости у контроллеров.</span><span class="sxs-lookup"><span data-stu-id="275ed-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="275ed-132">[Замените контейнер внедрения зависимостей по умолчанию на стороннюю реализацию](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="275ed-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="275ed-133">Авторы: [Shadi Namrouti](https://github.com/shadinamrouti) (Шади Намрути), [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Стив Смит](https://github.com/ardalis) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="275ed-133">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="275ed-134">Контроллеры ASP.NET Core MVC запрашивают зависимости явно с помощью конструкторов.</span><span class="sxs-lookup"><span data-stu-id="275ed-134">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="275ed-135">ASP.NET Core имеет встроенную поддержку [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="275ed-135">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="275ed-136">Внедрение зависимостей упрощает тестирование и поддержку приложений.</span><span class="sxs-lookup"><span data-stu-id="275ed-136">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="275ed-137">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="275ed-137">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="275ed-138">Внедрение через конструктор</span><span class="sxs-lookup"><span data-stu-id="275ed-138">Constructor Injection</span></span>

<span data-ttu-id="275ed-139">Службы добавляются в качестве параметра конструктора, и среда выполнения разрешает службу из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="275ed-139">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="275ed-140">Службы обычно задаются с помощью интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="275ed-140">Services are typically defined using interfaces.</span></span> <span data-ttu-id="275ed-141">Например, рассмотрим приложение, которому требуется текущее время.</span><span class="sxs-lookup"><span data-stu-id="275ed-141">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="275ed-142">Следующие интерфейсы предоставляют службу `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="275ed-142">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="275ed-143">В следующем коде реализуется интерфейс `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="275ed-143">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="275ed-144">Добавьте службу в контейнер службы:</span><span class="sxs-lookup"><span data-stu-id="275ed-144">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="275ed-145">Дополнительные сведения о <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, см. в разделе [Время существования службы внедрения зависимости](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="275ed-145">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="275ed-146">Следующий код отображает приветствие пользователю в зависимости от времени дня:</span><span class="sxs-lookup"><span data-stu-id="275ed-146">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="275ed-147">Запустите приложение, и в зависимости от времени отобразится сообщение.</span><span class="sxs-lookup"><span data-stu-id="275ed-147">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="275ed-148">Внедрение действий с помощью FromServices</span><span class="sxs-lookup"><span data-stu-id="275ed-148">Action injection with FromServices</span></span>

<span data-ttu-id="275ed-149"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> позволяет внедрять службу непосредственно в метод действия без использования внедрения через конструктор:</span><span class="sxs-lookup"><span data-stu-id="275ed-149">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="275ed-150">Доступ к параметрам из контроллера</span><span class="sxs-lookup"><span data-stu-id="275ed-150">Access settings from a controller</span></span>

<span data-ttu-id="275ed-151">Доступ к параметрам приложения или конфигурации из контроллера относится к типичным шаблонам.</span><span class="sxs-lookup"><span data-stu-id="275ed-151">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="275ed-152">*Шаблон параметров*, описанный в разделе <xref:fundamentals/configuration/options>, является предпочтительным подходом для управления параметрами.</span><span class="sxs-lookup"><span data-stu-id="275ed-152">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="275ed-153">Как правило, не следует напрямую внедрять <xref:Microsoft.Extensions.Configuration.IConfiguration> в контроллер.</span><span class="sxs-lookup"><span data-stu-id="275ed-153">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="275ed-154">Создайте класс, представляющий параметры.</span><span class="sxs-lookup"><span data-stu-id="275ed-154">Create a class that represents the options.</span></span> <span data-ttu-id="275ed-155">Например:</span><span class="sxs-lookup"><span data-stu-id="275ed-155">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="275ed-156">Добавьте класс конфигурации в коллекцию служб:</span><span class="sxs-lookup"><span data-stu-id="275ed-156">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="275ed-157">Настройте приложение для чтения параметров из файла формата JSON:</span><span class="sxs-lookup"><span data-stu-id="275ed-157">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="275ed-158">Следующий код запрашивает параметры `IOptions<SampleWebSettings>` из контейнера служб и использует их в методе `Index`:</span><span class="sxs-lookup"><span data-stu-id="275ed-158">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="275ed-159">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="275ed-159">Additional resources</span></span>

* <span data-ttu-id="275ed-160">См. раздел <xref:mvc/controllers/testing>, чтобы узнать, как упростить тестирование кода, явно запросив зависимости у контроллеров.</span><span class="sxs-lookup"><span data-stu-id="275ed-160">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="275ed-161">[Замените контейнер внедрения зависимостей по умолчанию на стороннюю реализацию](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="275ed-161">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end