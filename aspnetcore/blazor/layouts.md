---
title: Макеты Blazor в ASP.NET Core
author: guardrex
description: Узнайте, как создавать многократно используемые компоненты макета для Blazor приложений.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5c6771dd7249bfb8280ba20e1ce75967f279971c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771589"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="d5792-103">Макеты Blazor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5792-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="d5792-104">Авторы: [Райнер Стропек](https://www.timecockpit.com) (Rainer Stropek) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="d5792-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d5792-105">Некоторые элементы приложения, такие как меню, сообщения об авторских правах и логотипы компании, обычно являются частью общего макета приложения и используются каждым компонентом в приложении.</span><span class="sxs-lookup"><span data-stu-id="d5792-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="d5792-106">Копирование кода этих элементов во все компоненты приложения не является эффективным подходом &mdash; каждый раз, когда одному из элементов требуется обновление, требуется обновлять каждый компонент.</span><span class="sxs-lookup"><span data-stu-id="d5792-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="d5792-107">Такое дублирование сложно поддерживать, и это может привести к несогласованному содержимому с течением времени.</span><span class="sxs-lookup"><span data-stu-id="d5792-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="d5792-108">Для решения этой проблемы используются *макеты*.</span><span class="sxs-lookup"><span data-stu-id="d5792-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="d5792-109">Технически макет представляет собой просто другой компонент.</span><span class="sxs-lookup"><span data-stu-id="d5792-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="d5792-110">Макет определяется в шаблоне Razor или в коде C# и может использовать [привязки данных](xref:blazor/data-binding), [внедрения зависимостей](xref:blazor/dependency-injection) и другие сценарии компонентов.</span><span class="sxs-lookup"><span data-stu-id="d5792-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="d5792-111">Чтобы превратить *компонент* в *макет*, компонент:</span><span class="sxs-lookup"><span data-stu-id="d5792-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="d5792-112">Наследует от `LayoutComponentBase`, который определяет свойство `Body` для отображаемого содержимого внутри макета.</span><span class="sxs-lookup"><span data-stu-id="d5792-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="d5792-113">Использует синтаксис `@Body` Razor для указания расположения в разметке макета, в которой отображается содержимое.</span><span class="sxs-lookup"><span data-stu-id="d5792-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="d5792-114">В следующем примере кода показан шаблон Razor компонента макета *MainLayout.razor*.</span><span class="sxs-lookup"><span data-stu-id="d5792-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="d5792-115">Макет наследует `LayoutComponentBase` и задает `@Body` между панелью навигации и нижним колонтитулом:</span><span class="sxs-lookup"><span data-stu-id="d5792-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="d5792-116">В приложении на основе одного из шаблонов приложений Blazor компонент `MainLayout` (*MainLayout.razor*) находится в папке *Shared* приложения.</span><span class="sxs-lookup"><span data-stu-id="d5792-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="d5792-117">Макет по умолчанию</span><span class="sxs-lookup"><span data-stu-id="d5792-117">Default layout</span></span>

<span data-ttu-id="d5792-118">Укажите макет приложения по умолчанию в компоненте `Router` в файле *App.razor* приложения.</span><span class="sxs-lookup"><span data-stu-id="d5792-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="d5792-119">Следующий компонент `Router`, предоставляемый шаблонами Blazor по умолчанию, задает для макета по умолчанию компонент `MainLayout`:</span><span class="sxs-lookup"><span data-stu-id="d5792-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="d5792-120">Чтобы предоставить макет по умолчанию для содержимого `NotFound`, укажите `LayoutView` для содержимого `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="d5792-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="d5792-121">Дополнительные сведения о компоненте `Router` см. в разделе <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="d5792-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="d5792-122">Указание макета в качестве макета по умолчанию в маршрутизаторе достаточно полезно, так как в этом случае его можно переопределить отдельно для каждого компонента или папки.</span><span class="sxs-lookup"><span data-stu-id="d5792-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="d5792-123">Для настройки макета приложения по умолчанию предпочтительнее использовать маршрутизатор, поскольку это наиболее общий способ.</span><span class="sxs-lookup"><span data-stu-id="d5792-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="d5792-124">Указание макета в компоненте</span><span class="sxs-lookup"><span data-stu-id="d5792-124">Specify a layout in a component</span></span>

<span data-ttu-id="d5792-125">Используйте директиву Razor `@layout`, чтобы применить макет к компоненту.</span><span class="sxs-lookup"><span data-stu-id="d5792-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="d5792-126">Компилятор преобразует `@layout` в `LayoutAttribute`, который применяется к классу компонента.</span><span class="sxs-lookup"><span data-stu-id="d5792-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="d5792-127">Содержимое следующего компонента `MasterList` вставляется в `MasterLayout` в позиции `@Body`:</span><span class="sxs-lookup"><span data-stu-id="d5792-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="d5792-128">Указание макета непосредственно в компоненте переопределяет *макет по умолчанию*, заданный в маршрутизаторе, или директиву `@layout`, импортированную из файла *_Imports.razor*.</span><span class="sxs-lookup"><span data-stu-id="d5792-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="d5792-129">Централизованный выбор макетов</span><span class="sxs-lookup"><span data-stu-id="d5792-129">Centralized layout selection</span></span>

<span data-ttu-id="d5792-130">В каждой папке приложения может дополнительно содержаться файл шаблона с именем *_Imports.razor*.</span><span class="sxs-lookup"><span data-stu-id="d5792-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="d5792-131">Компилятор включает директивы, указанные в файле импорта, во все шаблоны Razor в одной и той же папке и рекурсивно во всех ее вложенных папках.</span><span class="sxs-lookup"><span data-stu-id="d5792-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="d5792-132">Таким образом, файл *_Imports.razor*, содержащий `@layout MyCoolLayout`, гарантирует, что все компоненты в папке используют `MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="d5792-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="d5792-133">Нет необходимости многократно добавлять `@layout MyCoolLayout` во все файлы *RAZOR* в папке и вложенных папках.</span><span class="sxs-lookup"><span data-stu-id="d5792-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="d5792-134">Директивы `@using` применяются к компонентам таким же образом.</span><span class="sxs-lookup"><span data-stu-id="d5792-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="d5792-135">Следующий файл *_Imports.razor*:</span><span class="sxs-lookup"><span data-stu-id="d5792-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="d5792-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="d5792-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="d5792-137">Все компоненты Razor в одной и той же папке и во всех вложенных папках.</span><span class="sxs-lookup"><span data-stu-id="d5792-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="d5792-138">Пространство имен `BlazorApp1.Data` .</span><span class="sxs-lookup"><span data-stu-id="d5792-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="d5792-139">Файл *_Imports.razor* аналогичен файлу [_ViewImports.cshtml для представлений и страниц Razor](xref:mvc/views/layout#importing-shared-directives), однако он применяется специально к файлам компонентов Razor.</span><span class="sxs-lookup"><span data-stu-id="d5792-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="d5792-140">Указание макета в файле *_Imports.razor* переопределяет макет, указанный в качестве *макета по умолчанию* маршрутизатора.</span><span class="sxs-lookup"><span data-stu-id="d5792-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="d5792-141">Вложенные макеты</span><span class="sxs-lookup"><span data-stu-id="d5792-141">Nested layouts</span></span>

<span data-ttu-id="d5792-142">Приложения могут состоять из вложенных макетов.</span><span class="sxs-lookup"><span data-stu-id="d5792-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="d5792-143">Компонент может ссылаться на макет, который, в свою очередь, ссылается на другой макет.</span><span class="sxs-lookup"><span data-stu-id="d5792-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="d5792-144">Например, для создания структуры многоуровневого меню используются вложенные макеты.</span><span class="sxs-lookup"><span data-stu-id="d5792-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="d5792-145">В следующем примере показано использование вложенных макетов.</span><span class="sxs-lookup"><span data-stu-id="d5792-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="d5792-146">Файл *EpisodesComponent.razor* является отображаемым компонентом.</span><span class="sxs-lookup"><span data-stu-id="d5792-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="d5792-147">Компонент ссылается на `MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="d5792-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="d5792-148">Файл *MasterListLayout.razor* предоставляет `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="d5792-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="d5792-149">Макет ссылается на другой макет, `MasterLayout`, где он преобразуется для просмотра.</span><span class="sxs-lookup"><span data-stu-id="d5792-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="d5792-150">`EpisodesComponent` отображается там, где находится `@Body`:</span><span class="sxs-lookup"><span data-stu-id="d5792-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="d5792-151">Наконец, `MasterLayout` в файле *MasterLayout.razor* содержит элементы макета верхнего уровня, такие как заголовок, главное меню и нижний колонтитул.</span><span class="sxs-lookup"><span data-stu-id="d5792-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="d5792-152">`MasterListLayout` с `EpisodesComponent` отображается там, где находится `@Body`:</span><span class="sxs-lookup"><span data-stu-id="d5792-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="d5792-153">Совместное использование макета Razor Pages с интегрированными компонентами</span><span class="sxs-lookup"><span data-stu-id="d5792-153">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="d5792-154">Если маршрутизируемые компоненты интегрированы в приложение Razor Pages, общий макет приложения можно использовать с компонентами.</span><span class="sxs-lookup"><span data-stu-id="d5792-154">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="d5792-155">Для получения дополнительной информации см. <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="d5792-155">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5792-156">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d5792-156">Additional resources</span></span>

* <xref:mvc/views/layout>
