---
title: Совместное использование контроллеров Razor , представлений, страниц и других приложений с частями приложения в ASP.NET Core
author: rick-anderson
description: Совместное использование контроллеров Razor , представлений, страниц и других приложений с частями приложения в ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 68991a3df5e09b63dc52bdadae55f055a721ad3c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774409"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a><span data-ttu-id="59626-103">Совместное использование контроллеров Razor , представлений, страниц и других приложений с частями приложения</span><span class="sxs-lookup"><span data-stu-id="59626-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="59626-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="59626-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="59626-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="59626-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="59626-106">*Часть приложения* — это абстракция для ресурсов приложения.</span><span class="sxs-lookup"><span data-stu-id="59626-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="59626-107">Части приложений позволяют ASP.NET Core обнаруживать контроллеры, просматривать компоненты, вспомогательные функции тегов, Razor страницы, источники компиляции Razor и многое другое.</span><span class="sxs-lookup"><span data-stu-id="59626-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="59626-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> является частью приложения.</span><span class="sxs-lookup"><span data-stu-id="59626-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="59626-109">`AssemblyPart` инкапсулирует ссылку на сборку и предоставляет типы и ссылки на компиляцию.</span><span class="sxs-lookup"><span data-stu-id="59626-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="59626-110">[Поставщики компонентов](#fp) работают с частями приложения для заполнения компонентов приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="59626-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="59626-111">Основной вариант использования частей приложения заключается в настройке приложения для обнаружения (или запрета загрузки) компонентов ASP.NET Core из сборки.</span><span class="sxs-lookup"><span data-stu-id="59626-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="59626-112">Например, может потребоваться совместное использование общих функциональных возможностей несколькими приложениями.</span><span class="sxs-lookup"><span data-stu-id="59626-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="59626-113">С помощью частей приложения можно совместно использовать сборку (библиотеку DLL), содержащую контроллеры, Razor представления, страницы, источники компиляций Razor, вспомогательные функции тегов и многое другое, с несколькими приложениями.</span><span class="sxs-lookup"><span data-stu-id="59626-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="59626-114">Желательно предоставить общий доступ к сборке для дублирования кода в нескольких проектах.</span><span class="sxs-lookup"><span data-stu-id="59626-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="59626-115">Приложения ASP.NET Core загружают компоненты из <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="59626-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="59626-116">Класс <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> представляет часть приложения, поддерживаемую сборкой.</span><span class="sxs-lookup"><span data-stu-id="59626-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="59626-117">Загрузка компонентов ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59626-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="59626-118">Используйте классы <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> и <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> для обнаружения и загрузки компонентов ASP.NET Core (контроллеры, компоненты представления и т. д.).</span><span class="sxs-lookup"><span data-stu-id="59626-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="59626-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> отслеживает доступные части приложения и поставщики компонентов.</span><span class="sxs-lookup"><span data-stu-id="59626-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="59626-120">Функция `ApplicationPartManager` настраивается в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="59626-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="59626-121">Следующий код предоставляет альтернативный подход к настройке `ApplicationPartManager` с использованием `AssemblyPart`:</span><span class="sxs-lookup"><span data-stu-id="59626-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="59626-122">Предыдущие два примера кода загружают `SharedController` из сборки.</span><span class="sxs-lookup"><span data-stu-id="59626-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="59626-123">`SharedController` не находится в проекте приложения.</span><span class="sxs-lookup"><span data-stu-id="59626-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="59626-124">См. пример [решения WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) для загрузки.</span><span class="sxs-lookup"><span data-stu-id="59626-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="59626-125">Включение представлений</span><span class="sxs-lookup"><span data-stu-id="59626-125">Include views</span></span>

<span data-ttu-id="59626-126">Используйте [ Razor библиотеку классов](xref:razor-pages/ui-class) для включения представлений в сборку.</span><span class="sxs-lookup"><span data-stu-id="59626-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="59626-127">Запрет загрузки ресурсов</span><span class="sxs-lookup"><span data-stu-id="59626-127">Prevent loading resources</span></span>

<span data-ttu-id="59626-128">Части приложения можно использовать для *запрета* загрузки ресурсов в определенной сборке или расположении.</span><span class="sxs-lookup"><span data-stu-id="59626-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="59626-129">Добавьте или удалите элементы коллекции <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>, чтобы скрыть ресурсы или сделать их доступными.</span><span class="sxs-lookup"><span data-stu-id="59626-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="59626-130">Порядок записей в коллекции `ApplicationParts` не имеет значения.</span><span class="sxs-lookup"><span data-stu-id="59626-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="59626-131">Настройте класс `ApplicationPartManager` перед его использованием для конфигурации служб в контейнере.</span><span class="sxs-lookup"><span data-stu-id="59626-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="59626-132">Например, настройте класс `ApplicationPartManager` перед вызовом метода `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="59626-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="59626-133">Вызовите метод `Remove` в коллекции `ApplicationParts`, чтобы удалить ресурс.</span><span class="sxs-lookup"><span data-stu-id="59626-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="59626-134">`ApplicationPartManager` содержит части для:</span><span class="sxs-lookup"><span data-stu-id="59626-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="59626-135">сборки приложения и зависимых сборок;</span><span class="sxs-lookup"><span data-stu-id="59626-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="59626-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="59626-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="59626-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="59626-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="59626-138">Поставщики компонентов</span><span class="sxs-lookup"><span data-stu-id="59626-138">Feature providers</span></span>

<span data-ttu-id="59626-139">Поставщики компонентов приложений проверяют части приложения и предоставляют для них компоненты.</span><span class="sxs-lookup"><span data-stu-id="59626-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="59626-140">Доступны встроенные поставщики компонентов для следующих компонентов ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="59626-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="59626-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="59626-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="59626-142">Поставщики компонентов наследуют от <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, где `T` является типом компонента.</span><span class="sxs-lookup"><span data-stu-id="59626-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="59626-143">Поставщики компонентов можно реализовать для любого из перечисленных выше типов компонентов.</span><span class="sxs-lookup"><span data-stu-id="59626-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="59626-144">Порядок поставщиков компонентов в `ApplicationPartManager.FeatureProviders` может повлиять на поведение во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="59626-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="59626-145">Поставщики, добавленные позднее, могут реагировать на действия, выполняемые ранее добавленными поставщиками.</span><span class="sxs-lookup"><span data-stu-id="59626-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="59626-146">отображение доступных компонентов</span><span class="sxs-lookup"><span data-stu-id="59626-146">Display available features</span></span>

<span data-ttu-id="59626-147">Компоненты, доступные для приложения, можно перечислить путем запроса `ApplicationPartManager` с помощью [внедрения зависимостей](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="59626-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="59626-148">В [примере для скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) используется приведенный выше код для отображения компонентов приложения:</span><span class="sxs-lookup"><span data-stu-id="59626-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="59626-149">Обнаружение в частях приложения</span><span class="sxs-lookup"><span data-stu-id="59626-149">Discovery in application parts</span></span>

<span data-ttu-id="59626-150">Ошибки HTTP 404 нередко возникают при разработке с использованием частей приложений.</span><span class="sxs-lookup"><span data-stu-id="59626-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="59626-151">Обычно эти ошибки вызваны отсутствием существенного требования к способу обнаружения частей приложений.</span><span class="sxs-lookup"><span data-stu-id="59626-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="59626-152">Если приложение возвращает ошибку HTTP 404, убедитесь, что выполнены следующие требования:</span><span class="sxs-lookup"><span data-stu-id="59626-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="59626-153">Для параметра `applicationName` должна быть задана корневая сборка, используемая для обнаружения.</span><span class="sxs-lookup"><span data-stu-id="59626-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="59626-154">Корневая сборка, используемая для обнаружения, обычно является сборкой точки входа.</span><span class="sxs-lookup"><span data-stu-id="59626-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="59626-155">Корневая сборка должна иметь ссылку на части, используемые для обнаружения.</span><span class="sxs-lookup"><span data-stu-id="59626-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="59626-156">Ссылка может быть прямой или транзитивной.</span><span class="sxs-lookup"><span data-stu-id="59626-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="59626-157">Корневая сборка должна ссылаться на веб-пакет SDK.</span><span class="sxs-lookup"><span data-stu-id="59626-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="59626-158">Платформа имеет логику, которая помечает в корневой сборке атрибуты, используемые для обнаружения.</span><span class="sxs-lookup"><span data-stu-id="59626-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="59626-159">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="59626-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="59626-160">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="59626-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="59626-161">*Часть приложения* — это абстракция для ресурсов приложения.</span><span class="sxs-lookup"><span data-stu-id="59626-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="59626-162">Части приложений позволяют ASP.NET Core обнаруживать контроллеры, просматривать компоненты, вспомогательные функции тегов, Razor страницы, источники компиляции Razor и многое другое.</span><span class="sxs-lookup"><span data-stu-id="59626-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="59626-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) — это часть приложения.</span><span class="sxs-lookup"><span data-stu-id="59626-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="59626-164">`AssemblyPart` инкапсулирует ссылку на сборку и предоставляет типы и ссылки на компиляцию.</span><span class="sxs-lookup"><span data-stu-id="59626-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="59626-165">*Поставщики компонентов* работают с частями приложения для заполнения компонентов приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="59626-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="59626-166">Основной вариант использования частей приложения заключается в настройке приложения для обнаружения (или запрета загрузки) компонентов ASP.NET Core из сборки.</span><span class="sxs-lookup"><span data-stu-id="59626-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="59626-167">Например, может потребоваться совместное использование общих функциональных возможностей несколькими приложениями.</span><span class="sxs-lookup"><span data-stu-id="59626-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="59626-168">С помощью частей приложения можно совместно использовать сборку (библиотеку DLL), содержащую контроллеры, Razor представления, страницы, источники компиляций Razor, вспомогательные функции тегов и многое другое, с несколькими приложениями.</span><span class="sxs-lookup"><span data-stu-id="59626-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="59626-169">Желательно предоставить общий доступ к сборке для дублирования кода в нескольких проектах.</span><span class="sxs-lookup"><span data-stu-id="59626-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="59626-170">Приложения ASP.NET Core загружают компоненты из <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="59626-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="59626-171">Класс <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> представляет часть приложения, поддерживаемую сборкой.</span><span class="sxs-lookup"><span data-stu-id="59626-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="59626-172">Загрузка компонентов ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59626-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="59626-173">Используйте классы `ApplicationPart` и `AssemblyPart` для обнаружения и загрузки компонентов ASP.NET Core (контроллеры, компоненты представления и т. д.).</span><span class="sxs-lookup"><span data-stu-id="59626-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="59626-174"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> отслеживает доступные части приложения и поставщики компонентов.</span><span class="sxs-lookup"><span data-stu-id="59626-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="59626-175">Функция `ApplicationPartManager` настраивается в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="59626-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="59626-176">Следующий код предоставляет альтернативный подход к настройке `ApplicationPartManager` с использованием `AssemblyPart`:</span><span class="sxs-lookup"><span data-stu-id="59626-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="59626-177">Предыдущие два примера кода загружают `SharedController` из сборки.</span><span class="sxs-lookup"><span data-stu-id="59626-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="59626-178">`SharedController` не находится в проекте приложения.</span><span class="sxs-lookup"><span data-stu-id="59626-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="59626-179">См. пример [решения WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) для загрузки.</span><span class="sxs-lookup"><span data-stu-id="59626-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="59626-180">Включение представлений</span><span class="sxs-lookup"><span data-stu-id="59626-180">Include views</span></span>

<span data-ttu-id="59626-181">Используйте [ Razor библиотеку классов](xref:razor-pages/ui-class) для включения представлений в сборку.</span><span class="sxs-lookup"><span data-stu-id="59626-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="59626-182">Запрет загрузки ресурсов</span><span class="sxs-lookup"><span data-stu-id="59626-182">Prevent loading resources</span></span>

<span data-ttu-id="59626-183">Части приложения можно использовать для *запрета* загрузки ресурсов в определенной сборке или расположении.</span><span class="sxs-lookup"><span data-stu-id="59626-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="59626-184">Добавьте или удалите элементы коллекции <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>, чтобы скрыть ресурсы или сделать их доступными.</span><span class="sxs-lookup"><span data-stu-id="59626-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="59626-185">Порядок записей в коллекции `ApplicationParts` не имеет значения.</span><span class="sxs-lookup"><span data-stu-id="59626-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="59626-186">Настройте класс `ApplicationPartManager` перед его использованием для конфигурации служб в контейнере.</span><span class="sxs-lookup"><span data-stu-id="59626-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="59626-187">Например, настройте класс `ApplicationPartManager` перед вызовом метода `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="59626-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="59626-188">Вызовите метод `Remove` в коллекции `ApplicationParts`, чтобы удалить ресурс.</span><span class="sxs-lookup"><span data-stu-id="59626-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="59626-189">В следующем коде для удаления `MyDependentLibrary` из приложения используется <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="59626-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="59626-190">`ApplicationPartManager` содержит части для:</span><span class="sxs-lookup"><span data-stu-id="59626-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="59626-191">сборки приложения и зависимых сборок;</span><span class="sxs-lookup"><span data-stu-id="59626-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="59626-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="59626-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="59626-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="59626-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="59626-194">Поставщики компонентов приложений</span><span class="sxs-lookup"><span data-stu-id="59626-194">Application feature providers</span></span>

<span data-ttu-id="59626-195">Поставщики компонентов приложений проверяют части приложения и предоставляют для них компоненты.</span><span class="sxs-lookup"><span data-stu-id="59626-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="59626-196">Доступны встроенные поставщики компонентов для следующих компонентов ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="59626-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="59626-197">Контроллеры</span><span class="sxs-lookup"><span data-stu-id="59626-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="59626-198">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="59626-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="59626-199">Просмотр компонентов</span><span class="sxs-lookup"><span data-stu-id="59626-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="59626-200">Поставщики компонентов наследуют от <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, где `T` является типом компонента.</span><span class="sxs-lookup"><span data-stu-id="59626-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="59626-201">Поставщики компонентов можно реализовать для любого из перечисленных выше типов компонентов.</span><span class="sxs-lookup"><span data-stu-id="59626-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="59626-202">Порядок поставщиков компонентов в `ApplicationPartManager.FeatureProviders` может повлиять на поведение во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="59626-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="59626-203">Поставщики, добавленные позднее, могут реагировать на действия, выполняемые ранее добавленными поставщиками.</span><span class="sxs-lookup"><span data-stu-id="59626-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="59626-204">отображение доступных компонентов</span><span class="sxs-lookup"><span data-stu-id="59626-204">Display available features</span></span>

<span data-ttu-id="59626-205">Компоненты, доступные для приложения, можно перечислить путем запроса `ApplicationPartManager` с помощью [внедрения зависимостей](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="59626-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="59626-206">В [примере для скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) используется приведенный выше код для отображения компонентов приложения:</span><span class="sxs-lookup"><span data-stu-id="59626-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="59626-207">Обнаружение в частях приложения</span><span class="sxs-lookup"><span data-stu-id="59626-207">Discovery in application parts</span></span>

<span data-ttu-id="59626-208">Ошибки HTTP 404 нередко возникают при разработке с использованием частей приложений.</span><span class="sxs-lookup"><span data-stu-id="59626-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="59626-209">Обычно эти ошибки вызваны отсутствием существенного требования к способу обнаружения частей приложений.</span><span class="sxs-lookup"><span data-stu-id="59626-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="59626-210">Если приложение возвращает ошибку HTTP 404, убедитесь, что выполнены следующие требования:</span><span class="sxs-lookup"><span data-stu-id="59626-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="59626-211">Для параметра `applicationName` должна быть задана корневая сборка, используемая для обнаружения.</span><span class="sxs-lookup"><span data-stu-id="59626-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="59626-212">Корневая сборка, используемая для обнаружения, обычно является сборкой точки входа.</span><span class="sxs-lookup"><span data-stu-id="59626-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="59626-213">Корневая сборка должна иметь ссылку на части, используемые для обнаружения.</span><span class="sxs-lookup"><span data-stu-id="59626-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="59626-214">Ссылка может быть прямой или транзитивной.</span><span class="sxs-lookup"><span data-stu-id="59626-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="59626-215">Корневая сборка должна ссылаться на веб-пакет SDK.</span><span class="sxs-lookup"><span data-stu-id="59626-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="59626-216">Платформа ASP.NET Core имеет специальную логику, которая помечает в корневой сборке атрибуты, используемые для обнаружения.</span><span class="sxs-lookup"><span data-stu-id="59626-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
