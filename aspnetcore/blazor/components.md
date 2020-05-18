---
title: Создание и использование компонентов Razor ASP.NET Core
author: guardrex
description: Сведения о том, как создавать и использовать компоненты Razor, включая способы привязки к данным, обработки событий и управления жизненным циклом компонентов.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: a7009bf1cf99a15f3617b47a904d52f5787b9ce1
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153518"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="16ae9-103">Создание и использование компонентов Razor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="16ae9-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="16ae9-104">Авторы: [Люк Латэм (Luke Latham)](https://github.com/guardrex), [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и Тобиас Бартщ [(Tobias Bartsch)](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="16ae9-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="16ae9-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="16ae9-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="16ae9-106">Приложения Blazor создаются с использованием *компонентов*.</span><span class="sxs-lookup"><span data-stu-id="16ae9-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="16ae9-107">Компонент — это автономный блок пользовательского интерфейса, такой как страница, диалоговое окно или форма.</span><span class="sxs-lookup"><span data-stu-id="16ae9-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="16ae9-108">Компонент включает разметку HTML и логику обработки, необходимую для внедрения данных или реагирования на события пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="16ae9-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="16ae9-109">Компоненты являются гибкими и облегченными.</span><span class="sxs-lookup"><span data-stu-id="16ae9-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="16ae9-110">Их можно вкладывать, использовать повторно и сразу в нескольких проектах.</span><span class="sxs-lookup"><span data-stu-id="16ae9-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="16ae9-111">Классы компонентов</span><span class="sxs-lookup"><span data-stu-id="16ae9-111">Component classes</span></span>

<span data-ttu-id="16ae9-112">Компоненты реализуются в файлах компонентов [Razor](xref:mvc/views/razor) ( *.razor*) с помощью комбинации разметки HTML и C#.</span><span class="sxs-lookup"><span data-stu-id="16ae9-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="16ae9-113">Компонент в Blazor формально называется *компонентом Razor* .</span><span class="sxs-lookup"><span data-stu-id="16ae9-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="16ae9-114">Имя компонента должно начинаться с заглавной буквы.</span><span class="sxs-lookup"><span data-stu-id="16ae9-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="16ae9-115">Например, *MyCoolComponent.razor* является допустимым, а *myCoolComponent.razor* нет.</span><span class="sxs-lookup"><span data-stu-id="16ae9-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="16ae9-116">Пользовательский интерфейс для компонента определяется с помощью HTML.</span><span class="sxs-lookup"><span data-stu-id="16ae9-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="16ae9-117">Логика динамического отображения (например, выражения, циклы и условные выражения) добавляется с помощью встроенного синтаксиса C# под названием [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="16ae9-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="16ae9-118">Во время компиляции приложения разметка HTML и логика отрисовки C# преобразуются в класс компонента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="16ae9-119">Имя создаваемого класса соответствует имени файла.</span><span class="sxs-lookup"><span data-stu-id="16ae9-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="16ae9-120">Элементы класса компонента определяются в блоке `@code`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="16ae9-121">В блоке `@code` указываются состояние (свойства, поля) компонента и методы для обработки событий или определения другой логики компонента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="16ae9-122">Допускается более одного блока `@code`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="16ae9-123">Элементы компонента можно использовать как часть логики отрисовки компонента с использованием выражений C#, начинающихся с `@`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="16ae9-124">Например, поле C# отрисовывается путем добавления `@` к имени поля.</span><span class="sxs-lookup"><span data-stu-id="16ae9-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="16ae9-125">В следующем примере вычисляется и отрисовывается следующее:</span><span class="sxs-lookup"><span data-stu-id="16ae9-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="16ae9-126">`headingFontStyle` в значении свойства CSS для `font-style`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-126">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="16ae9-127">`headingText` в содержимом элемента `<h1>`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-127">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="16ae9-128">После первоначальной отрисовки компонента он повторно создает дерево отрисовки в ответ на события.</span><span class="sxs-lookup"><span data-stu-id="16ae9-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="16ae9-129">Затем Blazor сравнивает новое и прежнее дерево отрисовки и применяет все изменения в модели DOM браузера.</span><span class="sxs-lookup"><span data-stu-id="16ae9-129">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="16ae9-130">Компоненты являются обычными классами C# и могут размещаться в любом месте внутри проекта.</span><span class="sxs-lookup"><span data-stu-id="16ae9-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="16ae9-131">Компоненты, создающие веб-страницы, обычно находятся в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="16ae9-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="16ae9-132">Компоненты, не связанные со страницами, часто находятся в папке *Shared* или настраиваемой папке, добавленной в проект.</span><span class="sxs-lookup"><span data-stu-id="16ae9-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="16ae9-133">Как правило, пространство имен компонента является производным от корневого пространства имен приложения и расположения компонента (папки) в приложении.</span><span class="sxs-lookup"><span data-stu-id="16ae9-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="16ae9-134">Если корневым пространством имен приложения является `BlazorApp`, а компонент `Counter` находится в папке *Pages*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="16ae9-135">Пространством имен компонента `Counter` является `BlazorApp.Pages`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="16ae9-136">Полным именем компонента является `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="16ae9-137">При использовании пользовательских папок, содержащих компоненты, добавьте инструкцию `using` в родительский компонент или в файл *_Imports.razor* приложения.</span><span class="sxs-lookup"><span data-stu-id="16ae9-137">For custom folders that hold components, add a `using` statement to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="16ae9-138">В следующем примере становятся доступными компоненты в папке *Компоненты*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-138">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="16ae9-139">Кроме того, на компонент можно ссылаться напрямую:</span><span class="sxs-lookup"><span data-stu-id="16ae9-139">Alternatively, a component can be directly referenced:</span></span>

```razor
<BlazorApp.Components.MyCoolComponent />
```

<span data-ttu-id="16ae9-140">Дополнительные сведения см. в разделе [Импорт компонентов](#import-components).</span><span class="sxs-lookup"><span data-stu-id="16ae9-140">For more information, see the [Import components](#import-components) section.</span></span>

## <a name="static-assets"></a><span data-ttu-id="16ae9-141">Статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="16ae9-141">Static assets</span></span>

Blazor<span data-ttu-id="16ae9-142"> соответствует соглашению для приложений ASP.NET Core о размещении статических ресурсов в [корневом веб-каталоге (wwwroot)](xref:fundamentals/index#web-root) проекта.</span><span class="sxs-lookup"><span data-stu-id="16ae9-142"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="16ae9-143">Используйте базовый относительный путь (`/`) для ссылки на корневой веб-каталог статического ресурса.</span><span class="sxs-lookup"><span data-stu-id="16ae9-143">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="16ae9-144">В следующем примере *logo.png* физически находится в папке *{PROJECT ROOT}/wwwroot/images*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-144">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="16ae9-145">Компоненты Razor **не** поддерживают нотацию тильды с косой чертой (`~/`).</span><span class="sxs-lookup"><span data-stu-id="16ae9-145">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="16ae9-146">Сведения о настройке базового пути приложения см. в разделе <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="16ae9-146">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="16ae9-147">Вспомогательные функции тегов в компонентах не поддерживаются</span><span class="sxs-lookup"><span data-stu-id="16ae9-147">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="16ae9-148">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) не поддерживаются в компонентах Razor (файлы *.razor*).</span><span class="sxs-lookup"><span data-stu-id="16ae9-148">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="16ae9-149">Чтобы обеспечить функциональные возможности, аналогичные вспомогательным функциям тегов, в Blazor, создайте компонент с теми же функциональными возможностями, что и вспомогательная функция тега, и используйте его вместо нее.</span><span class="sxs-lookup"><span data-stu-id="16ae9-149">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="16ae9-150">Использование компонентов</span><span class="sxs-lookup"><span data-stu-id="16ae9-150">Use components</span></span>

<span data-ttu-id="16ae9-151">Компоненты могут включать другие компоненты, объявляя их с помощью синтаксиса HTML-элементов.</span><span class="sxs-lookup"><span data-stu-id="16ae9-151">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="16ae9-152">Разметка для использования компонента выглядит как тег HTML с именем, соответствующем типу компонента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-152">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="16ae9-153">Следующая разметка в *Index.razor* визуализирует экземпляр `HeadingComponent`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-153">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="16ae9-154">*Components/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-154">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="16ae9-155">Если компонент содержит HTML-элемент с первой заглавной буквой, который не соответствует имени компонента, выдается предупреждение о том, что элемент имеет непредвиденное имя.</span><span class="sxs-lookup"><span data-stu-id="16ae9-155">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="16ae9-156">Добавление директивы `@using` для пространства имен компонента делает компонент доступным, что позволяет устранить это предупреждение.</span><span class="sxs-lookup"><span data-stu-id="16ae9-156">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="16ae9-157">Маршрутизация</span><span class="sxs-lookup"><span data-stu-id="16ae9-157">Routing</span></span>

<span data-ttu-id="16ae9-158">Маршрутизация в Blazor достигается путем предоставления шаблона маршрута каждому доступному компоненту в приложении.</span><span class="sxs-lookup"><span data-stu-id="16ae9-158">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="16ae9-159">При компиляции файла Razor с директивой `@page` созданному классу предоставляется атрибут <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, указывающий шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="16ae9-159">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="16ae9-160">Во время выполнения маршрутизатор ищет классы компонентов с `RouteAttribute` и отображает любой компонент, шаблон маршрута которого соответствует запрошенному URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="16ae9-160">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="16ae9-161">Для получения дополнительной информации см. <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="16ae9-161">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="16ae9-162">Параметры</span><span class="sxs-lookup"><span data-stu-id="16ae9-162">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="16ae9-163">Параметры маршрута</span><span class="sxs-lookup"><span data-stu-id="16ae9-163">Route parameters</span></span>

<span data-ttu-id="16ae9-164">Компоненты могут принимать параметры маршрута из шаблона маршрута, указанного в директиве `@page`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-164">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="16ae9-165">Маршрутизатор использует параметры маршрута для заполнения соответствующих параметров компонента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-165">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="16ae9-166">*Pages/RouteParameter.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-166">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="16ae9-167">Необязательные параметры не поддерживаются, поэтому в предыдущем примере применяются две директивы `@page`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-167">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="16ae9-168">Первая позволяет переходить к компоненту без параметра.</span><span class="sxs-lookup"><span data-stu-id="16ae9-168">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="16ae9-169">Вторая директива `@page` принимает параметр маршрута `{text}` и присваивает значение свойству `Text`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-169">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="16ae9-170">Синтаксис *универсального* параметра (`*`/`**`) **не** поддерживается в компонентах Razor ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="16ae9-170">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="16ae9-171">Параметры компонентов</span><span class="sxs-lookup"><span data-stu-id="16ae9-171">Component parameters</span></span>

<span data-ttu-id="16ae9-172">Компоненты могут иметь *параметры*, определяемые с помощью открытых свойств в классе компонента с атрибутом `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-172">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="16ae9-173">Используйте атрибуты, чтобы указать аргументы для компонента в разметке.</span><span class="sxs-lookup"><span data-stu-id="16ae9-173">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="16ae9-174">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-174">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="16ae9-175">В следующем примере из примера приложения `ParentComponent` задает значение свойства `Title` для `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-175">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="16ae9-176">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-176">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="16ae9-177">Не создавайте компоненты, записывающие их в собственные *параметры компонентов*, — используйте вместо этого закрытое поле.</span><span class="sxs-lookup"><span data-stu-id="16ae9-177">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="16ae9-178">Дополнительные сведения см. в разделе [Не создавать компоненты, которые записываются в собственные свойства параметров](#dont-create-components-that-write-to-their-own-parameter-properties).</span><span class="sxs-lookup"><span data-stu-id="16ae9-178">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="16ae9-179">Дочернее содержимое</span><span class="sxs-lookup"><span data-stu-id="16ae9-179">Child content</span></span>

<span data-ttu-id="16ae9-180">Компоненты могут задавать содержимое другого компонента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-180">Components can set the content of another component.</span></span> <span data-ttu-id="16ae9-181">Назначение компонента задает содержимое между тегами, указывающими принимающий компонент.</span><span class="sxs-lookup"><span data-stu-id="16ae9-181">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="16ae9-182">В следующем примере `ChildComponent` имеет свойство `ChildContent`, представляющее `RenderFragment`, который представляет сегмент пользовательского интерфейса для отрисовки.</span><span class="sxs-lookup"><span data-stu-id="16ae9-182">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="16ae9-183">Значение `ChildContent` находится в том месте разметки компонента, где должно быть визуализировано содержимое.</span><span class="sxs-lookup"><span data-stu-id="16ae9-183">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="16ae9-184">Значение `ChildContent` принимается от родительского компонента и визуализируется внутри `panel-body` панели начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="16ae9-184">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="16ae9-185">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-185">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="16ae9-186">Свойству, принимающему содержимое `RenderFragment`, по соглашению необходимо присвоить имя `ChildContent`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-186">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="16ae9-187">`ParentComponent` в примере приложения может предоставить содержимое для отрисовки `ChildComponent`, заключив содержимое в теги `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-187">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="16ae9-188">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-188">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="16ae9-189">Сплаттинг атрибутов и произвольные параметры</span><span class="sxs-lookup"><span data-stu-id="16ae9-189">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="16ae9-190">Компоненты могут записывать и визуализировать дополнительные атрибуты в дополнение к объявленным параметрам компонента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-190">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="16ae9-191">Можно записать дополнительные атрибуты в словарь, а затем выполнить *сплаттинг* для элемента при подготовке отрисовки компонента с помощью директивы [`@attributes`](xref:mvc/views/razor#attributes) Razor.</span><span class="sxs-lookup"><span data-stu-id="16ae9-191">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="16ae9-192">Этот сценарий полезен при определении компонента, который создает элемент разметки, поддерживающий разнообразные настройки.</span><span class="sxs-lookup"><span data-stu-id="16ae9-192">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="16ae9-193">Например, может оказаться утомительным по отдельности определять атрибуты для `<input>`, поддерживающего много параметров.</span><span class="sxs-lookup"><span data-stu-id="16ae9-193">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="16ae9-194">В следующем примере первый элемент `<input>` (`id="useIndividualParams"`) использует отдельные параметры компонента, а второй элемент `<input>` (`id="useAttributesDict"`) использует сплаттинг атрибутов:</span><span class="sxs-lookup"><span data-stu-id="16ae9-194">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="16ae9-195">Тип параметра должен реализовывать `IEnumerable<KeyValuePair<string, object>>` с ключами строки.</span><span class="sxs-lookup"><span data-stu-id="16ae9-195">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="16ae9-196">В этом сценарии также можно использовать `IReadOnlyDictionary<string, object>`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-196">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="16ae9-197">При использовании обоих подходов получаются идентичные отрисованные элементы `<input>`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-197">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="16ae9-198">Чтобы принять произвольные атрибуты, определите параметр компонента с помощью атрибута `[Parameter]` со свойством `CaptureUnmatchedValues`, имеющим значение `true`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-198">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="16ae9-199">Свойство `CaptureUnmatchedValues` в `[Parameter]` позволяет параметру соответствовать всем атрибутам, которые не соответствуют никакому другому параметру.</span><span class="sxs-lookup"><span data-stu-id="16ae9-199">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="16ae9-200">Компонент может определять только один параметр с `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-200">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="16ae9-201">Тип свойства, используемый с `CaptureUnmatchedValues`, должен быть назначаемым из `Dictionary<string, object>` с ключами строки.</span><span class="sxs-lookup"><span data-stu-id="16ae9-201">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="16ae9-202">В этом сценарии также можно использовать `IEnumerable<KeyValuePair<string, object>>` или `IReadOnlyDictionary<string, object>`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-202">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="16ae9-203">Расположение `@attributes` относительно положения атрибутов элемента имеет значение.</span><span class="sxs-lookup"><span data-stu-id="16ae9-203">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="16ae9-204">Когда выполняется сплаттинг `@attributes` для элемента, атрибуты обрабатываются справа налево (от последнего к первому).</span><span class="sxs-lookup"><span data-stu-id="16ae9-204">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="16ae9-205">Рассмотрим следующий пример компонента, использующего компонент `Child`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-205">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="16ae9-206">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-206">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="16ae9-207">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-207">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="16ae9-208">Атрибут `extra` компонента `Child` стоит справа от `@attributes`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-208">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="16ae9-209">`<div>`, визуализируемый компонентом `Parent`, содержит `extra="5"` при передаче через дополнительный атрибут, так как атрибуты обрабатываются справа налево (от последнего к первому):</span><span class="sxs-lookup"><span data-stu-id="16ae9-209">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="16ae9-210">В следующем примере порядок `extra` и `@attributes` в `<div>` компонента `Child` изменен на противоположный:</span><span class="sxs-lookup"><span data-stu-id="16ae9-210">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="16ae9-211">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-211">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="16ae9-212">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-212">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="16ae9-213">Визуализируемый `<div>` в компоненте `Parent` содержит `extra="10"` при передаче через дополнительный атрибут:</span><span class="sxs-lookup"><span data-stu-id="16ae9-213">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="16ae9-214">Запись ссылок на компоненты</span><span class="sxs-lookup"><span data-stu-id="16ae9-214">Capture references to components</span></span>

<span data-ttu-id="16ae9-215">Ссылки на компоненты позволяют ссылаться на экземпляр компонента, чтобы можно было выполнять команды для этого экземпляра, например `Show` или `Reset`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-215">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="16ae9-216">Чтобы записать ссылку на компонент, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="16ae9-216">To capture a component reference:</span></span>

* <span data-ttu-id="16ae9-217">Добавьте к дочернему компоненту атрибут [`@ref`](xref:mvc/views/razor#ref).</span><span class="sxs-lookup"><span data-stu-id="16ae9-217">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="16ae9-218">Определите поле с тем же типом, что и у дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-218">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="16ae9-219">При отрисовке компонента поле `loginDialog` заполняется экземпляром дочернего компонента `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-219">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="16ae9-220">Затем можно вызывать методы .NET в экземпляре компонента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-220">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="16ae9-221">Переменная `loginDialog` заполняется только после отрисовки компонента, а ее выходные данные включают элемент `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-221">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="16ae9-222">До этого момента ссылаться не на что.</span><span class="sxs-lookup"><span data-stu-id="16ae9-222">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="16ae9-223">Для управления ссылками на компоненты после завершения отрисовки компонента используйте [методы OnAfterRenderAsync или OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="16ae9-223">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="16ae9-224">Сведения о ссылках на компоненты в цикле см. в разделе [Получение ссылок на несколько схожих дочерних компонентов (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="16ae9-224">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="16ae9-225">При записи ссылок на компоненты используется аналогичный синтаксис для [записи ссылок на элементы](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), это не функция взаимодействия JavaScript.</span><span class="sxs-lookup"><span data-stu-id="16ae9-225">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="16ae9-226">Ссылки на компоненты не передаются в код JavaScript &mdash; они используются только в коде .NET.</span><span class="sxs-lookup"><span data-stu-id="16ae9-226">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="16ae9-227">**Не** используйте ссылки на компоненты для изменения состояния дочерних компонентов.</span><span class="sxs-lookup"><span data-stu-id="16ae9-227">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="16ae9-228">Вместо этого используйте обычные декларативные параметры для передачи данных дочерним компонентам.</span><span class="sxs-lookup"><span data-stu-id="16ae9-228">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="16ae9-229">Использование обычных декларативных параметров приводит к тому, что дочерние компоненты автоматически визуализируются в нужное время.</span><span class="sxs-lookup"><span data-stu-id="16ae9-229">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="16ae9-230">Внешний вызов методов компонента для изменения состояния</span><span class="sxs-lookup"><span data-stu-id="16ae9-230">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="16ae9-231"> использует контекст синхронизации (`SynchronizationContext`) для принудительного использования одного логического потока выполнения.</span><span class="sxs-lookup"><span data-stu-id="16ae9-231"> uses a synchronization context (`SynchronizationContext`) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="16ae9-232">[Методы жизненного цикла ](xref:blazor/lifecycle) компонента и все обратные вызовы событий, сделанные Blazor, выполняются в этом контексте синхронизации.</span><span class="sxs-lookup"><span data-stu-id="16ae9-232">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="16ae9-233">Контекст синхронизации Blazor Server пытается эмулировать однопоточную среду таким образом, чтобы она точно соответствовала модели WebAssembly в браузере, которая является однопоточной.</span><span class="sxs-lookup"><span data-stu-id="16ae9-233">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="16ae9-234">В любой момент времени работа выполняется только в одном потоке, что создает впечатление единого логического потока.</span><span class="sxs-lookup"><span data-stu-id="16ae9-234">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="16ae9-235">Две операции не могут выполняться одновременно.</span><span class="sxs-lookup"><span data-stu-id="16ae9-235">No two operations execute concurrently.</span></span>

<span data-ttu-id="16ae9-236">Если компонент нужно изменить на основе внешнего события, такого как таймер или другие уведомления, используйте метод `InvokeAsync`, который будет выполнять отправку в контекст синхронизации Blazor.</span><span class="sxs-lookup"><span data-stu-id="16ae9-236">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's synchronization context.</span></span> <span data-ttu-id="16ae9-237">Например, рассмотрим *службу уведомителя*, которая может уведомлять любой компонент, ожидающий передачи данных, об измененном состоянии:</span><span class="sxs-lookup"><span data-stu-id="16ae9-237">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="16ae9-238">Зарегистрируйте службу `NotifierService` как одноэлементную:</span><span class="sxs-lookup"><span data-stu-id="16ae9-238">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="16ae9-239">В Blazor WebAssembly зарегистрируйте службу в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-239">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="16ae9-240">В Blazor Server зарегистрируйте службу в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-240">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="16ae9-241">Используйте `NotifierService` для изменения компонента:</span><span class="sxs-lookup"><span data-stu-id="16ae9-241">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="16ae9-242">В предыдущем примере `NotifierService` вызывает метод `OnNotify` компонента вне контекста синхронизации Blazor.</span><span class="sxs-lookup"><span data-stu-id="16ae9-242">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="16ae9-243">`InvokeAsync` используется для переключения на подходящий контекст и постановки отрисовки в очередь.</span><span class="sxs-lookup"><span data-stu-id="16ae9-243">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="16ae9-244">Использование \@key для управления сохранением элементов и компонентов</span><span class="sxs-lookup"><span data-stu-id="16ae9-244">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="16ae9-245">При отрисовке списка элементов или компонентов и последующем изменении элементов или компонентов алгоритм сравнения Blazor должен решить, какие из предыдущих элементов или компонентов можно оставить и как следует сопоставить с ними объекты модели.</span><span class="sxs-lookup"><span data-stu-id="16ae9-245">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="16ae9-246">Обычно этот процесс выполняется автоматически, и его можно игнорировать, но в некоторых случаях может потребоваться управлять данным процессом.</span><span class="sxs-lookup"><span data-stu-id="16ae9-246">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="16ae9-247">Рассмотрим следующий пример.</span><span class="sxs-lookup"><span data-stu-id="16ae9-247">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="16ae9-248">Содержимое коллекции `People` может изменяться при вставке, удалении или повторном упорядочении записей.</span><span class="sxs-lookup"><span data-stu-id="16ae9-248">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="16ae9-249">Когда компонент отрисовывается повторно, компонент `<DetailsEditor>` может измениться на получение других значений параметра `Details`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-249">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="16ae9-250">Это может усложнить повторную отрисовку.</span><span class="sxs-lookup"><span data-stu-id="16ae9-250">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="16ae9-251">В некоторых случаях повторная отрисовка может привести к появлению заметных различий в поведении, таких как потеря фокуса элемента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-251">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="16ae9-252">Процесс сопоставления можно контролировать с помощью атрибута директивы [`@key`](xref:mvc/views/razor#key).</span><span class="sxs-lookup"><span data-stu-id="16ae9-252">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="16ae9-253">`@key` заставляет алгоритм сравнения гарантировать сохранение элементов или компонентов на основе значения ключа:</span><span class="sxs-lookup"><span data-stu-id="16ae9-253">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="16ae9-254">При изменении коллекции `People` алгоритм сравнения сохраняет связь между экземплярами `<DetailsEditor>` и экземплярами `person`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-254">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="16ae9-255">Если `Person` удаляется из списка `People`, то из пользовательского интерфейса удаляется только соответствующий экземпляр `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-255">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="16ae9-256">Другие экземпляры остаются без изменений.</span><span class="sxs-lookup"><span data-stu-id="16ae9-256">Other instances are left unchanged.</span></span>
* <span data-ttu-id="16ae9-257">Если в какой-либо позиции в списке вставляется `Person`, то в соответствующей позиции вставляется один новый экземпляр `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-257">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="16ae9-258">Другие экземпляры остаются без изменений.</span><span class="sxs-lookup"><span data-stu-id="16ae9-258">Other instances are left unchanged.</span></span>
* <span data-ttu-id="16ae9-259">При переупорядочении записей `Person` соответствующие экземпляры `<DetailsEditor>` сохраняются и переупорядочиваются в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="16ae9-259">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="16ae9-260">В некоторых сценариях использование `@key` минимизирует сложность повторной отрисовки и предотвращает потенциальные проблемы с изменяющимися элементами модели DOM с отслеживанием состояния, например положением фокуса.</span><span class="sxs-lookup"><span data-stu-id="16ae9-260">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="16ae9-261">Ключи являются локальными для каждого компонента или элемента контейнера.</span><span class="sxs-lookup"><span data-stu-id="16ae9-261">Keys are local to each container element or component.</span></span> <span data-ttu-id="16ae9-262">Ключи не сравниваются глобально по всему документу.</span><span class="sxs-lookup"><span data-stu-id="16ae9-262">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="16ae9-263">Условия для использования \@key</span><span class="sxs-lookup"><span data-stu-id="16ae9-263">When to use \@key</span></span>

<span data-ttu-id="16ae9-264">Как правило, `@key` имеет смысл использовать при отрисовке списка (например, в блоке `@foreach`) и при наличии подходящего значения для определения `@key`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-264">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="16ae9-265">Можно также использовать `@key`, чтобы запретить Blazor сохранять поддерево элементов или компонентов при изменении объекта:</span><span class="sxs-lookup"><span data-stu-id="16ae9-265">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="16ae9-266">Если `@currentPerson` меняется, директива атрибута `@key` заставляет Blazor отменить весь `<div>` и его потомков и перестроить поддерево в пользовательском интерфейсе с использованием новых элементов и компонентов.</span><span class="sxs-lookup"><span data-stu-id="16ae9-266">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="16ae9-267">Это может быть полезно, если нужно гарантировать, что при изменении `@currentPerson` состояние пользовательского интерфейса не сохраняется.</span><span class="sxs-lookup"><span data-stu-id="16ae9-267">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="16ae9-268">Условия для отказа от использования \@key</span><span class="sxs-lookup"><span data-stu-id="16ae9-268">When not to use \@key</span></span>

<span data-ttu-id="16ae9-269">Сравнение с использованием `@key` подразумевает определенное снижение производительности.</span><span class="sxs-lookup"><span data-stu-id="16ae9-269">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="16ae9-270">Это снижение производительности не слишком велико, однако указывать `@key` следует только в том случае, если управление правилами сохранения элементов или компонентов выгодно для приложения.</span><span class="sxs-lookup"><span data-stu-id="16ae9-270">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="16ae9-271">Даже если `@key` не используется, Blazor сохраняет экземпляры дочерних элементов и компонентов в максимально возможной степени.</span><span class="sxs-lookup"><span data-stu-id="16ae9-271">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="16ae9-272">Единственным преимуществом использования `@key` является контроль над тем, *как* экземпляры модели сопоставляются с сохраненными экземплярами компонентов, вместо выбора сопоставления с помощью алгоритма сравнения.</span><span class="sxs-lookup"><span data-stu-id="16ae9-272">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="16ae9-273">Значения, которые следует использовать для \@key</span><span class="sxs-lookup"><span data-stu-id="16ae9-273">What values to use for \@key</span></span>

<span data-ttu-id="16ae9-274">Как правило, для `@key` имеет смысл указать значение одного из следующих видов:</span><span class="sxs-lookup"><span data-stu-id="16ae9-274">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="16ae9-275">Экземпляры объектов модели (например, экземпляр `Person`, как в предыдущем примере).</span><span class="sxs-lookup"><span data-stu-id="16ae9-275">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="16ae9-276">Это гарантирует сохранение на основе равенства ссылок на объекты.</span><span class="sxs-lookup"><span data-stu-id="16ae9-276">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="16ae9-277">Уникальные идентификаторы (например, значения первичного ключа типа `int`, `string` или `Guid`).</span><span class="sxs-lookup"><span data-stu-id="16ae9-277">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="16ae9-278">Убедитесь, что значения, используемые для `@key`, не конфликтуют.</span><span class="sxs-lookup"><span data-stu-id="16ae9-278">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="16ae9-279">Если в одном родительском элементе обнаруживаются конфликтующие значения, Blazor выдает исключение, поскольку не может детерминированно сопоставлять старые элементы или компоненты с новыми.</span><span class="sxs-lookup"><span data-stu-id="16ae9-279">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="16ae9-280">Используйте только уникальные значения, такие как экземпляры объекта или значения первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="16ae9-280">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="16ae9-281">Не создавать компоненты, которые записываются в собственные свойства параметров</span><span class="sxs-lookup"><span data-stu-id="16ae9-281">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="16ae9-282">Параметры перезаписываются при следующих условиях.</span><span class="sxs-lookup"><span data-stu-id="16ae9-282">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="16ae9-283">Содержимое дочернего компонента подготавливается к просмотру с помощью `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-283">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="16ae9-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается в родительском компоненте.</span><span class="sxs-lookup"><span data-stu-id="16ae9-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="16ae9-285">Параметры сбрасываются, так как родительский компонент перерисовывается при вызове <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>, а новые значения параметров передаются дочернему компоненту.</span><span class="sxs-lookup"><span data-stu-id="16ae9-285">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="16ae9-286">Рассмотрим следующий компонент `Expander`, который:</span><span class="sxs-lookup"><span data-stu-id="16ae9-286">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="16ae9-287">преобразует дочернее содержимое;</span><span class="sxs-lookup"><span data-stu-id="16ae9-287">Renders child content.</span></span>
* <span data-ttu-id="16ae9-288">переключает отображение дочернего содержимого с помощью параметра компонента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-288">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="16ae9-289">Компонент `Expander` добавляется в родительский компонент, который может вызывать `StateHasChanged`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-289">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="16ae9-290">Изначально компоненты `Expander` работают независимо, когда их свойства `Expanded` переключаются.</span><span class="sxs-lookup"><span data-stu-id="16ae9-290">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="16ae9-291">Дочерние компоненты сохраняют свои состояния, как и ожидалось.</span><span class="sxs-lookup"><span data-stu-id="16ae9-291">The child components maintain their states as expected.</span></span> <span data-ttu-id="16ae9-292">Когда в родительском элементе вызывается `StateHasChanged`, параметр `Expanded` первого дочернего компонента сбрасывается обратно к первоначальному значению (`true`).</span><span class="sxs-lookup"><span data-stu-id="16ae9-292">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="16ae9-293">Значение `Expanded` второго компонента `Expander` не сбрасывается, так как во втором компоненте не отображается дочернее содержимое.</span><span class="sxs-lookup"><span data-stu-id="16ae9-293">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="16ae9-294">Чтобы сохранить состояние в предыдущем сценарии, используйте *закрытое поле* в компоненте `Expander`, чтобы сохранить состояние переключения.</span><span class="sxs-lookup"><span data-stu-id="16ae9-294">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="16ae9-295">Приведенный ниже компонент `Expander` делает следующее.</span><span class="sxs-lookup"><span data-stu-id="16ae9-295">The following `Expander` component:</span></span>

* <span data-ttu-id="16ae9-296">Принимает значение параметра компонента `Expanded` из родительского элемента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-296">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="16ae9-297">Присваивает значение параметра компонента *закрытому полю* (`expanded`) при [событии OnInitialized](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="16ae9-297">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="16ae9-298">Использует закрытое поле для поддержания внутреннего состояния переключения.</span><span class="sxs-lookup"><span data-stu-id="16ae9-298">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a><span data-ttu-id="16ae9-299">Поддержка разделяемых классов</span><span class="sxs-lookup"><span data-stu-id="16ae9-299">Partial class support</span></span>

<span data-ttu-id="16ae9-300">Компоненты Razor создаются как разделяемые классы.</span><span class="sxs-lookup"><span data-stu-id="16ae9-300">Razor components are generated as partial classes.</span></span> <span data-ttu-id="16ae9-301">Создавать компоненты Razor можно одним из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="16ae9-301">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="16ae9-302">Код C# определяется в блоке [`@code`](xref:mvc/views/razor#code) с разметкой HTML и кодом Razor в одном файле.</span><span class="sxs-lookup"><span data-stu-id="16ae9-302">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="16ae9-303">Шаблоны Blazor определяют свои компоненты Razor с помощью этого подхода.</span><span class="sxs-lookup"><span data-stu-id="16ae9-303">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="16ae9-304">Код C# помещается в файл кода программной части, определенный как разделяемый класс.</span><span class="sxs-lookup"><span data-stu-id="16ae9-304">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="16ae9-305">В следующем примере показан компонент `Counter` по умолчанию с блоком `@code` в приложении, созданном из шаблона Blazor.</span><span class="sxs-lookup"><span data-stu-id="16ae9-305">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="16ae9-306">Разметка HTML, код Razor и код C# находятся в одном файле.</span><span class="sxs-lookup"><span data-stu-id="16ae9-306">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="16ae9-307">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-307">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="16ae9-308">Компонент `Counter` также можно создать, используя файл кода программной части с разделяемым классом:</span><span class="sxs-lookup"><span data-stu-id="16ae9-308">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="16ae9-309">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-309">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="16ae9-310">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-310">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="16ae9-311">При необходимости добавьте в файл разделяемого класса нужные пространства имен.</span><span class="sxs-lookup"><span data-stu-id="16ae9-311">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="16ae9-312">К типичным пространствам имен, используемым компонентами Razor, относятся следующие.</span><span class="sxs-lookup"><span data-stu-id="16ae9-312">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="16ae9-313">Указание базового класса</span><span class="sxs-lookup"><span data-stu-id="16ae9-313">Specify a base class</span></span>

<span data-ttu-id="16ae9-314">Директиву [`@inherits`](xref:mvc/views/razor#inherits) можно использовать для указания базового класса для компонента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-314">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="16ae9-315">В следующем примере показано, как компонент может наследовать базовый класс `BlazorRocksBase`, чтобы предоставить свойства и методы компонента.</span><span class="sxs-lookup"><span data-stu-id="16ae9-315">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="16ae9-316">Базовый класс должен быть производным от `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-316">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="16ae9-317">*Pages/BlazorRocks.razor*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-317">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="16ae9-318">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-318">*BlazorRocksBase.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a><span data-ttu-id="16ae9-319">Указание атрибута</span><span class="sxs-lookup"><span data-stu-id="16ae9-319">Specify an attribute</span></span>

<span data-ttu-id="16ae9-320">Атрибуты можно указать в компонентах Razor с помощью директивы [`@attribute`](xref:mvc/views/razor#attribute).</span><span class="sxs-lookup"><span data-stu-id="16ae9-320">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="16ae9-321">В следующем примере атрибут `[Authorize]` применяется к классу компонентов:</span><span class="sxs-lookup"><span data-stu-id="16ae9-321">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="16ae9-322">Импорт компонентов</span><span class="sxs-lookup"><span data-stu-id="16ae9-322">Import components</span></span>

<span data-ttu-id="16ae9-323">Пространство имен компонента, созданного с помощью Razor, основано на следующем (в порядке приоритета).</span><span class="sxs-lookup"><span data-stu-id="16ae9-323">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="16ae9-324">Назначение [`@namespace`](xref:mvc/views/razor#namespace) в разметке файла Razor ( *.razor*) (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="16ae9-324">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="16ae9-325">`RootNamespace` проекта в файле проекта (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="16ae9-325">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="16ae9-326">Имя проекта, полученное из имени файла проекта (*CSPROJ*), и путь из корневого каталога проекта к компоненту.</span><span class="sxs-lookup"><span data-stu-id="16ae9-326">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="16ae9-327">Например, платформа разрешает *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) в пространство имен `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-327">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="16ae9-328">Компоненты соответствуют правилам привязки имен C#.</span><span class="sxs-lookup"><span data-stu-id="16ae9-328">Components follow C# name binding rules.</span></span> <span data-ttu-id="16ae9-329">Для компонента `Index` в этом примере компонентами в области действия являются все компоненты:</span><span class="sxs-lookup"><span data-stu-id="16ae9-329">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="16ae9-330">в той же папке *Pages*;</span><span class="sxs-lookup"><span data-stu-id="16ae9-330">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="16ae9-331">в корневой папке проекта, которая не задает другое пространство имен явным образом.</span><span class="sxs-lookup"><span data-stu-id="16ae9-331">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="16ae9-332">Компоненты, определенные в другом пространстве имен, передаются в область действия с помощью директивы [`@using`](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="16ae9-332">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="16ae9-333">Если в папке *BlazorSample/Shared/* существует другой компонент `NavMenu.razor`, его можно использовать в `Index.razor` с помощью следующего оператора `@using`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-333">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="16ae9-334">На компоненты также можно ссылаться с помощью полных имен, для чего не требуется директива [`@using`](xref:mvc/views/razor#using):</span><span class="sxs-lookup"><span data-stu-id="16ae9-334">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="16ae9-335">Квалификация `global::` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="16ae9-335">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="16ae9-336">Импорт компонентов с операторами `using` с псевдонимами (например, `@using Foo = Bar`) не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="16ae9-336">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="16ae9-337">Частично определенные имена не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="16ae9-337">Partially qualified names aren't supported.</span></span> <span data-ttu-id="16ae9-338">Например, добавление `@using BlazorSample` и ссылка на `NavMenu.razor` с помощью `<Shared.NavMenu></Shared.NavMenu>` не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="16ae9-338">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="16ae9-339">Условные атрибуты элемента HTML</span><span class="sxs-lookup"><span data-stu-id="16ae9-339">Conditional HTML element attributes</span></span>

<span data-ttu-id="16ae9-340">Атрибуты элемента HTML условно визуализируются в зависимости от значения .NET.</span><span class="sxs-lookup"><span data-stu-id="16ae9-340">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="16ae9-341">Если значение равно `false` или `null`, то атрибут не визуализируется.</span><span class="sxs-lookup"><span data-stu-id="16ae9-341">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="16ae9-342">Если значение равно `true`, атрибут визуализируется в свернутом виде.</span><span class="sxs-lookup"><span data-stu-id="16ae9-342">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="16ae9-343">В следующем примере `IsCompleted` определяет, визуализируется ли `checked` в разметке элемента:</span><span class="sxs-lookup"><span data-stu-id="16ae9-343">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="16ae9-344">Если `IsCompleted` имеет значение `true`, флажок визуализируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="16ae9-344">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="16ae9-345">Если `IsCompleted` имеет значение `false`, флажок визуализируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="16ae9-345">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="16ae9-346">Для получения дополнительной информации см. <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="16ae9-346">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="16ae9-347">Некоторые атрибуты HTML, такие как [, aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), работают неправильно, если типом .NET является `bool`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-347">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="16ae9-348">В этих случаях используйте тип `string` вместо `bool`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-348">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="16ae9-349">Необработанный HTML-код</span><span class="sxs-lookup"><span data-stu-id="16ae9-349">Raw HTML</span></span>

<span data-ttu-id="16ae9-350">Строки обычно визуализируются с помощью текстовых узлов модели DOM, что означает, что любая разметка, которую они могут содержать, игнорируется и обрабатывается как текстовый литерал.</span><span class="sxs-lookup"><span data-stu-id="16ae9-350">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="16ae9-351">Для отрисовки необработанного HTML-кода заключите HTML-содержимое в значение `MarkupString`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-351">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="16ae9-352">Это значение анализируется как HTML или SVG и вставляется в модель DOM.</span><span class="sxs-lookup"><span data-stu-id="16ae9-352">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="16ae9-353">Отрисовка необработанного HTML-кода, созданного из любого ненадежного источника, является **угрозой безопасности**, и ее следует избегать!</span><span class="sxs-lookup"><span data-stu-id="16ae9-353">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="16ae9-354">В следующем примере показано использование типа `MarkupString` для добавления блока статического HTML-содержимого в визуализируемые выходные данные компонента:</span><span class="sxs-lookup"><span data-stu-id="16ae9-354">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="16ae9-355">Каскадные значения и параметры</span><span class="sxs-lookup"><span data-stu-id="16ae9-355">Cascading values and parameters</span></span>

<span data-ttu-id="16ae9-356">В некоторых сценариях неудобно передавать данные из компонента-предка в компонент-потомок, используя [параметры компонента](#component-parameters), особенно если имеется несколько уровней компонентов.</span><span class="sxs-lookup"><span data-stu-id="16ae9-356">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="16ae9-357">Каскадные значения и параметры позволяют решить эту проблему, предоставляя компоненту-предку удобный способ передать значение всем его компонентам-потомкам.</span><span class="sxs-lookup"><span data-stu-id="16ae9-357">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="16ae9-358">Каскадные значения и параметры также предоставляют подход для координации компонентов.</span><span class="sxs-lookup"><span data-stu-id="16ae9-358">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="16ae9-359">Пример темы</span><span class="sxs-lookup"><span data-stu-id="16ae9-359">Theme example</span></span>

<span data-ttu-id="16ae9-360">В следующем примере из примера приложения класс `ThemeInfo` указывает сведения о теме для передачи вниз по иерархии компонентов, чтобы все кнопки в пределах заданной части приложения использовали одинаковый стиль.</span><span class="sxs-lookup"><span data-stu-id="16ae9-360">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="16ae9-361">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="16ae9-361">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="16ae9-362">Компонент-предок может предоставлять каскадное значение с помощью компонента каскадного значения.</span><span class="sxs-lookup"><span data-stu-id="16ae9-362">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="16ae9-363">Компонент `CascadingValue` упаковывает поддерево иерархии компонентов и предоставляет одно значение всем компонентам в этом поддереве.</span><span class="sxs-lookup"><span data-stu-id="16ae9-363">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="16ae9-364">Например, пример приложения указывает сведения о теме (`ThemeInfo`) в одном из макетов приложения в виде каскадного параметра для всех компонентов, составляющих основной текст макета свойства `@Body`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-364">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="16ae9-365">`ButtonClass` присваивается значение `btn-success` в компоненте макета.</span><span class="sxs-lookup"><span data-stu-id="16ae9-365">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="16ae9-366">Любой компонент-потомок может использовать это свойство через каскадный объект `ThemeInfo`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-366">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="16ae9-367">Компонент `CascadingValuesParametersLayout`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-367">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="16ae9-368">Чтобы использовать каскадные значения, компоненты объявляют каскадные параметры с помощью атрибута `[CascadingParameter]`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-368">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="16ae9-369">Каскадные значения привязаны к каскадным параметрам по типу.</span><span class="sxs-lookup"><span data-stu-id="16ae9-369">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="16ae9-370">В примере приложения компонент `CascadingValuesParametersTheme` привязывает каскадное значение `ThemeInfo` к каскадному параметру.</span><span class="sxs-lookup"><span data-stu-id="16ae9-370">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="16ae9-371">Параметр используется для задания класса CSS для одной из кнопок, отображаемых компонентом.</span><span class="sxs-lookup"><span data-stu-id="16ae9-371">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="16ae9-372">Компонент `CascadingValuesParametersTheme`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-372">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="16ae9-373">Чтобы каскадировать несколько значений одного типа в одном поддереве, укажите уникальную строку `Name` для каждого компонента `CascadingValue` и его соответствующего `CascadingParameter`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-373">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="16ae9-374">В следующем примере два компонента `CascadingValue` каскадируют разные экземпляры `MyCascadingType` по имени:</span><span class="sxs-lookup"><span data-stu-id="16ae9-374">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="16ae9-375">В компоненте-потомке каскадные параметры получают значения из соответствующих каскадных значений в компоненте-предке по имени:</span><span class="sxs-lookup"><span data-stu-id="16ae9-375">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="16ae9-376">Пример TabSet</span><span class="sxs-lookup"><span data-stu-id="16ae9-376">TabSet example</span></span>

<span data-ttu-id="16ae9-377">Каскадные параметры также позволяют компонентам взаимодействовать в рамках иерархии компонентов.</span><span class="sxs-lookup"><span data-stu-id="16ae9-377">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="16ae9-378">Например, рассмотрим следующий пример *TabSet* в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="16ae9-378">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="16ae9-379">Пример приложения содержит интерфейс `ITab`, реализуемый вкладками:</span><span class="sxs-lookup"><span data-stu-id="16ae9-379">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="16ae9-380">Компонент `CascadingValuesParametersTabSet` использует компонент `TabSet`, содержащий несколько компонентов `Tab`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-380">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="16ae9-381">Дочерние компоненты `Tab` не передаются в `TabSet` в качестве параметров явным образом.</span><span class="sxs-lookup"><span data-stu-id="16ae9-381">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="16ae9-382">Вместо этого дочерние компоненты `Tab` являются частью дочернего содержимого `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-382">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="16ae9-383">Однако `TabSet` по-прежнему необходимо знать о каждом компоненте `Tab`, чтобы визуализировать заголовки и активную вкладку. Чтобы обеспечить такую координацию без дополнительного кода, компонент `TabSet` *может предоставить себя в качестве каскадного значения*, которое затем используется компонентами-потомками `Tab`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-383">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="16ae9-384">Компонент `TabSet`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-384">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="16ae9-385">Компоненты-потомки `Tab` захватывают содержащий `TabSet` в качестве каскадного параметра, поэтому компоненты `Tab` добавляются в `TabSet` и координируют, какая вкладка активна.</span><span class="sxs-lookup"><span data-stu-id="16ae9-385">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="16ae9-386">Компонент `Tab`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-386">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="16ae9-387">Шаблоны Razor</span><span class="sxs-lookup"><span data-stu-id="16ae9-387">Razor templates</span></span>

<span data-ttu-id="16ae9-388">Фрагменты отрисовки можно определить с помощью синтаксиса шаблонов Razor.</span><span class="sxs-lookup"><span data-stu-id="16ae9-388">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="16ae9-389">Шаблоны Razor позволяют определить фрагмент кода пользовательского интерфейса и подразумевают следующий формат.</span><span class="sxs-lookup"><span data-stu-id="16ae9-389">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="16ae9-390">В следующем примере показано, как указать значения `RenderFragment` и `RenderFragment<T>` и визуализировать шаблоны непосредственно в компоненте.</span><span class="sxs-lookup"><span data-stu-id="16ae9-390">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="16ae9-391">Фрагменты отрисовки также могут передаваться в качестве аргументов в [шаблонные компоненты](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="16ae9-391">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="16ae9-392">Визуализированные выходные данные предыдущего кода:</span><span class="sxs-lookup"><span data-stu-id="16ae9-392">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="16ae9-393">Изображения SVG</span><span class="sxs-lookup"><span data-stu-id="16ae9-393">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="16ae9-394">Так как Blazor отображает визуализирует HTML-код, поддерживаемые браузером изображения, включая изображения *SVG*, поддерживаются с помощью тега `<img>`:</span><span class="sxs-lookup"><span data-stu-id="16ae9-394">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="16ae9-395">Аналогичным образом изображения SVG поддерживаются в правилах CSS файла таблицы стилей (*CSS*):</span><span class="sxs-lookup"><span data-stu-id="16ae9-395">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="16ae9-396">Однако встроенная разметка SVG не поддерживается во всех сценариях.</span><span class="sxs-lookup"><span data-stu-id="16ae9-396">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="16ae9-397">Если поместить тег `<svg>` непосредственно в файл компонента (*RAZOR*), то базовая отрисовка изображений доступна, но многие расширенные сценарии пока не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="16ae9-397">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="16ae9-398">Например, теги `<use>` сейчас не учитываются, а с некоторыми тегами SVG невозможно использовать `@bind`.</span><span class="sxs-lookup"><span data-stu-id="16ae9-398">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="16ae9-399">В будущем выпуске мы планируем устранить эти ограничения.</span><span class="sxs-lookup"><span data-stu-id="16ae9-399">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16ae9-400">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="16ae9-400">Additional resources</span></span>

* <span data-ttu-id="16ae9-401"><xref:security/blazor/server/threat-mitigation> &ndash; содержит рекомендации по созданию приложений Blazor Server, которые должны состязаться в условиях нехватки ресурсов.</span><span class="sxs-lookup"><span data-stu-id="16ae9-401"><xref:security/blazor/server/threat-mitigation> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
