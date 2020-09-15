---
title: Создание и использование компонентов Razor ASP.NET Core
author: guardrex
description: Сведения о том, как создавать и использовать компоненты Razor, включая способы привязки к данным, обработки событий и управления жизненным циклом компонентов.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
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
uid: blazor/components/index
ms.openlocfilehash: be1584e72fc1504ac9f8ca10a6b084c95a579b5b
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009626"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a><span data-ttu-id="e1c27-103">Создание и использование компонентов Razor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1c27-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="e1c27-104">Авторы: [Люк Латэм (Luke Latham)](https://github.com/guardrex), [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и Тобиас Бартщ [(Tobias Bartsch)](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="e1c27-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="e1c27-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1c27-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e1c27-106">Приложения Blazor создаются с использованием *компонентов*.</span><span class="sxs-lookup"><span data-stu-id="e1c27-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="e1c27-107">Компонент — это автономный блок пользовательского интерфейса, такой как страница, диалоговое окно или форма.</span><span class="sxs-lookup"><span data-stu-id="e1c27-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="e1c27-108">Компонент включает разметку HTML и логику обработки, необходимую для внедрения данных или реагирования на события пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="e1c27-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="e1c27-109">Компоненты являются гибкими и облегченными.</span><span class="sxs-lookup"><span data-stu-id="e1c27-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="e1c27-110">Их можно вкладывать, использовать повторно и сразу в нескольких проектах.</span><span class="sxs-lookup"><span data-stu-id="e1c27-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="e1c27-111">Классы компонентов</span><span class="sxs-lookup"><span data-stu-id="e1c27-111">Component classes</span></span>

<span data-ttu-id="e1c27-112">Компоненты реализуются в файлах компонентов [Razor](xref:mvc/views/razor) (`.razor`) с помощью комбинации разметки HTML и C#.</span><span class="sxs-lookup"><span data-stu-id="e1c27-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="e1c27-113">Компонент в Blazor формально называется *компонентом Razor* .</span><span class="sxs-lookup"><span data-stu-id="e1c27-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="no-locrazor-syntax"></a><span data-ttu-id="e1c27-114">Синтаксис Razor</span><span class="sxs-lookup"><span data-stu-id="e1c27-114">Razor syntax</span></span>

<span data-ttu-id="e1c27-115">В компонентах Razor приложений Blazor часто используется синтаксис Razor.</span><span class="sxs-lookup"><span data-stu-id="e1c27-115">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="e1c27-116">Если вы не знакомы с языком разметки Razor, сначала прочтите статью <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="e1c27-116">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="e1c27-117">При доступе к содержимому с синтаксисом Razor обратите особое внимание на следующее:</span><span class="sxs-lookup"><span data-stu-id="e1c27-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="e1c27-118">[Директивы](xref:mvc/views/razor#directives) — это зарезервированные ключевые слова с префиксом `@`, которые обычно меняют то, как разметка компонента анализируется и функционирует.</span><span class="sxs-lookup"><span data-stu-id="e1c27-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="e1c27-119">[Атрибуты директивы](xref:mvc/views/razor#directive-attributes) — зарезервированные ключевые слова с префиксом `@`, которые обычно меняют то, как разметка компонента анализируется и функционирует.</span><span class="sxs-lookup"><span data-stu-id="e1c27-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="e1c27-120">Имена</span><span class="sxs-lookup"><span data-stu-id="e1c27-120">Names</span></span>

<span data-ttu-id="e1c27-121">Имя компонента должно начинаться с заглавной буквы.</span><span class="sxs-lookup"><span data-stu-id="e1c27-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="e1c27-122">Например, `MyCoolComponent.razor` допустимо использовать, а `myCoolComponent.razor` нет.</span><span class="sxs-lookup"><span data-stu-id="e1c27-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="e1c27-123">Маршрутизация</span><span class="sxs-lookup"><span data-stu-id="e1c27-123">Routing</span></span>

<span data-ttu-id="e1c27-124">Маршрутизация в Blazor достигается путем предоставления шаблона маршрута каждому доступному компоненту в приложении.</span><span class="sxs-lookup"><span data-stu-id="e1c27-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="e1c27-125">При компиляции файла Razor с директивой [`@page`][9] созданному классу предоставляется атрибут <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, указывающий шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="e1c27-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="e1c27-126">Во время выполнения маршрутизатор ищет классы компонентов с <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> и отображает любой компонент, шаблон маршрута которого соответствует запрошенному URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="e1c27-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="e1c27-127">Для получения дополнительной информации см. <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="e1c27-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="e1c27-128">разметку</span><span class="sxs-lookup"><span data-stu-id="e1c27-128">Markup</span></span>

<span data-ttu-id="e1c27-129">Пользовательский интерфейс для компонента определяется с помощью HTML.</span><span class="sxs-lookup"><span data-stu-id="e1c27-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="e1c27-130">Логика динамического отображения (например, выражения, циклы и условные выражения) добавляется с помощью встроенного синтаксиса C# под названием *Razor* .</span><span class="sxs-lookup"><span data-stu-id="e1c27-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="e1c27-131">Во время компиляции приложения разметка HTML и логика отрисовки C# преобразуются в класс компонента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="e1c27-132">Имя создаваемого класса соответствует имени файла.</span><span class="sxs-lookup"><span data-stu-id="e1c27-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="e1c27-133">Элементы класса компонента определяются в блоке [`@code`][1].</span><span class="sxs-lookup"><span data-stu-id="e1c27-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="e1c27-134">В блоке [`@code`][1] указываются состояние (свойства, поля) компонента и методы для обработки событий или определения другой логики компонента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="e1c27-135">Допускается использование нескольких блоков [`@code`][1].</span><span class="sxs-lookup"><span data-stu-id="e1c27-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="e1c27-136">Элементы компонента можно использовать как часть логики отрисовки компонента с использованием выражений C#, начинающихся с `@`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="e1c27-137">Например, поле C# отрисовывается путем добавления `@` к имени поля.</span><span class="sxs-lookup"><span data-stu-id="e1c27-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="e1c27-138">В следующем примере вычисляется и отрисовывается следующее:</span><span class="sxs-lookup"><span data-stu-id="e1c27-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="e1c27-139">`headingFontStyle` в значении свойства CSS для `font-style`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="e1c27-140">`headingText` в содержимом элемента `<h1>`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="e1c27-141">После первоначальной отрисовки компонента он повторно создает дерево отрисовки в ответ на события.</span><span class="sxs-lookup"><span data-stu-id="e1c27-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="e1c27-142">Затем Blazor сравнивает новое и прежнее дерево отрисовки и применяет все изменения в модели DOM браузера.</span><span class="sxs-lookup"><span data-stu-id="e1c27-142">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="e1c27-143">Компоненты являются обычными классами C# и могут размещаться в любом месте внутри проекта.</span><span class="sxs-lookup"><span data-stu-id="e1c27-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="e1c27-144">Компоненты, создающие веб-страницы, обычно находятся в папке `Pages`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="e1c27-145">Компоненты, не являющиеся страницами, часто находятся в папке `Shared` или пользовательской папке, добавленной в проект.</span><span class="sxs-lookup"><span data-stu-id="e1c27-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="e1c27-146">Пространства имен</span><span class="sxs-lookup"><span data-stu-id="e1c27-146">Namespaces</span></span>

<span data-ttu-id="e1c27-147">Как правило, пространство имен компонента является производным от корневого пространства имен приложения и расположения компонента (папки) в приложении.</span><span class="sxs-lookup"><span data-stu-id="e1c27-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="e1c27-148">Если пространством имен корня приложения является `BlazorSample`, а компонент `Counter` находится в папке `Pages`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="e1c27-149">Пространством имен компонента `Counter` является `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="e1c27-150">Полным именем компонента является `BlazorSample.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="e1c27-151">При использовании пользовательских папок, содержащих компоненты, добавьте директиву [`@using`][2] в родительский компонент или в файл `_Imports.razor` приложения.</span><span class="sxs-lookup"><span data-stu-id="e1c27-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="e1c27-152">В следующем примере становятся доступными компоненты в папке `Components`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="e1c27-153">На компоненты также можно ссылаться с помощью полных имен, для чего не требуется директива [`@using`][2]:</span><span class="sxs-lookup"><span data-stu-id="e1c27-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="e1c27-154">Пространство имен компонента, созданного с помощью Razor, основано на следующем (в порядке приоритета).</span><span class="sxs-lookup"><span data-stu-id="e1c27-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="e1c27-155">Назначение [`@namespace`][8] в разметке (`@namespace BlazorSample.MyNamespace`) файла Razor (`.razor`).</span><span class="sxs-lookup"><span data-stu-id="e1c27-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="e1c27-156">`RootNamespace` проекта в файле проекта (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="e1c27-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="e1c27-157">Имя проекта, полученное из имени файла проекта (`.csproj`), и путь из корневого каталога проекта к компоненту.</span><span class="sxs-lookup"><span data-stu-id="e1c27-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="e1c27-158">Например, платформа разрешает `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) в пространство имен `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="e1c27-159">Компоненты соответствуют правилам привязки имен C#.</span><span class="sxs-lookup"><span data-stu-id="e1c27-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="e1c27-160">Для компонента `Index` в этом примере компонентами в области действия являются все компоненты:</span><span class="sxs-lookup"><span data-stu-id="e1c27-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="e1c27-161">в этой же папке `Pages`;</span><span class="sxs-lookup"><span data-stu-id="e1c27-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="e1c27-162">в корневой папке проекта, которая не задает другое пространство имен явным образом.</span><span class="sxs-lookup"><span data-stu-id="e1c27-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="e1c27-163">Квалификация `global::` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="e1c27-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="e1c27-164">Импорт компонентов с инструкциями [`using`](/dotnet/csharp/language-reference/keywords/using-statement), содержащими псевдонимы, (например, `@using Foo = Bar`) не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="e1c27-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="e1c27-165">Частично определенные имена не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="e1c27-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="e1c27-166">Например, добавление `@using BlazorSample` и ссылка на компонент `NavMenu` (`NavMenu.razor`) с помощью `<Shared.NavMenu></Shared.NavMenu>` не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="e1c27-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="e1c27-167">Поддержка разделяемых классов</span><span class="sxs-lookup"><span data-stu-id="e1c27-167">Partial class support</span></span>

<span data-ttu-id="e1c27-168">Компоненты Razor создаются как разделяемые классы.</span><span class="sxs-lookup"><span data-stu-id="e1c27-168">Razor components are generated as partial classes.</span></span> <span data-ttu-id="e1c27-169">Создавать компоненты Razor можно одним из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="e1c27-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="e1c27-170">Код C# определяется в блоке [`@code`][1] с разметкой HTML и кодом Razor в одном файле.</span><span class="sxs-lookup"><span data-stu-id="e1c27-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="e1c27-171">Шаблоны Blazor определяют свои компоненты Razor с помощью этого подхода.</span><span class="sxs-lookup"><span data-stu-id="e1c27-171">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="e1c27-172">Код C# помещается в файл кода программной части, определенный как разделяемый класс.</span><span class="sxs-lookup"><span data-stu-id="e1c27-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="e1c27-173">В следующем примере показан компонент `Counter` по умолчанию с блоком [`@code`][1] в приложении, созданном из шаблона Blazor.</span><span class="sxs-lookup"><span data-stu-id="e1c27-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="e1c27-174">Разметка HTML, код Razor и код C# находятся в одном файле.</span><span class="sxs-lookup"><span data-stu-id="e1c27-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="e1c27-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-175">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="e1c27-176">Компонент `Counter` также можно создать, используя файл кода программной части с разделяемым классом:</span><span class="sxs-lookup"><span data-stu-id="e1c27-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="e1c27-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="e1c27-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-178">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
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

<span data-ttu-id="e1c27-179">При необходимости добавьте в файл разделяемого класса нужные пространства имен.</span><span class="sxs-lookup"><span data-stu-id="e1c27-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="e1c27-180">К типичным пространствам имен, используемым компонентами Razor, относятся следующие.</span><span class="sxs-lookup"><span data-stu-id="e1c27-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="e1c27-181">Директивы [`@using`][2] в файле `_Imports.razor` применяются только к файлам Razor (`.razor`), но не к файлам C# (`.cs`).</span><span class="sxs-lookup"><span data-stu-id="e1c27-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="e1c27-182">Указание базового класса</span><span class="sxs-lookup"><span data-stu-id="e1c27-182">Specify a base class</span></span>

<span data-ttu-id="e1c27-183">Директиву [`@inherits`][6] можно использовать для указания базового класса для компонента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="e1c27-184">В следующем примере показано, как компонент может наследовать базовый класс `BlazorRocksBase`, чтобы предоставить свойства и методы компонента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="e1c27-185">Базовый класс должен быть производным от <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="e1c27-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="e1c27-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="e1c27-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-187">`BlazorRocksBase.cs`:</span></span>

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

### <a name="use-components"></a><span data-ttu-id="e1c27-188">Использование компонентов</span><span class="sxs-lookup"><span data-stu-id="e1c27-188">Use components</span></span>

<span data-ttu-id="e1c27-189">Компоненты могут включать другие компоненты, объявляя их с помощью синтаксиса HTML-элементов.</span><span class="sxs-lookup"><span data-stu-id="e1c27-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="e1c27-190">Разметка для использования компонента выглядит как тег HTML с именем, соответствующем типу компонента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="e1c27-191">Следующая разметка в `Pages/Index.razor` преобразовывает экземпляр `HeadingComponent` для просмотра.</span><span class="sxs-lookup"><span data-stu-id="e1c27-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="e1c27-192">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-192">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="e1c27-193">Если компонент содержит HTML-элемент с первой заглавной буквой, который не соответствует имени компонента, выдается предупреждение о том, что элемент имеет непредвиденное имя.</span><span class="sxs-lookup"><span data-stu-id="e1c27-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="e1c27-194">Добавление директивы [`@using`][2] для пространства имен компонента делает компонент доступным, что позволяет устранить это предупреждение.</span><span class="sxs-lookup"><span data-stu-id="e1c27-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="e1c27-195">Параметры</span><span class="sxs-lookup"><span data-stu-id="e1c27-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="e1c27-196">Параметры маршрута</span><span class="sxs-lookup"><span data-stu-id="e1c27-196">Route parameters</span></span>

<span data-ttu-id="e1c27-197">Компоненты могут принимать параметры маршрута из шаблона маршрута, указанного в директиве [`@page`][9].</span><span class="sxs-lookup"><span data-stu-id="e1c27-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="e1c27-198">Маршрутизатор использует параметры маршрута для заполнения соответствующих параметров компонента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="e1c27-199">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-199">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="e1c27-200">Необязательные параметры не поддерживаются, поэтому в предыдущем примере применяются две директивы [`@page`][9].</span><span class="sxs-lookup"><span data-stu-id="e1c27-200">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="e1c27-201">Первая позволяет переходить к компоненту без параметра.</span><span class="sxs-lookup"><span data-stu-id="e1c27-201">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="e1c27-202">Вторая директива [`@page`][9] принимает параметр маршрута `{text}` и присваивает значение свойству `Text`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-202">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="e1c27-203">Сведения об универсальных параметрах маршрута (`{*pageRoute}`), которые захватывают пути в нескольких папках, см. в разделе <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="e1c27-203">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="e1c27-204">Параметры компонентов</span><span class="sxs-lookup"><span data-stu-id="e1c27-204">Component parameters</span></span>

<span data-ttu-id="e1c27-205">Компоненты могут иметь *параметры*, определяемые с помощью открытых свойств в классе компонента с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="e1c27-205">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="e1c27-206">Используйте атрибуты, чтобы указать аргументы для компонента в разметке.</span><span class="sxs-lookup"><span data-stu-id="e1c27-206">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="e1c27-207">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-207">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="e1c27-208">В следующем примере из примера приложения `ParentComponent` задает значение свойства `Title` для `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-208">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="e1c27-209">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-209">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="e1c27-210">Не создавайте компоненты, записывающие их в собственные *параметры компонентов*, — используйте вместо этого закрытое поле.</span><span class="sxs-lookup"><span data-stu-id="e1c27-210">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="e1c27-211">Дополнительные сведения см. в разделе [Перезаписанные параметры](#overwritten-parameters).</span><span class="sxs-lookup"><span data-stu-id="e1c27-211">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="e1c27-212">Дочернее содержимое</span><span class="sxs-lookup"><span data-stu-id="e1c27-212">Child content</span></span>

<span data-ttu-id="e1c27-213">Компоненты могут задавать содержимое другого компонента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-213">Components can set the content of another component.</span></span> <span data-ttu-id="e1c27-214">Назначение компонента задает содержимое между тегами, указывающими принимающий компонент.</span><span class="sxs-lookup"><span data-stu-id="e1c27-214">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="e1c27-215">В следующем примере `ChildComponent` имеет свойство `ChildContent`, представляющее <xref:Microsoft.AspNetCore.Components.RenderFragment>, который представляет сегмент пользовательского интерфейса для отрисовки.</span><span class="sxs-lookup"><span data-stu-id="e1c27-215">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="e1c27-216">Значение `ChildContent` находится в том месте разметки компонента, где должно быть визуализировано содержимое.</span><span class="sxs-lookup"><span data-stu-id="e1c27-216">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="e1c27-217">Значение `ChildContent` принимается от родительского компонента и визуализируется внутри `panel-body` панели начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="e1c27-217">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="e1c27-218">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-218">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="e1c27-219">Свойству, принимающему содержимое <xref:Microsoft.AspNetCore.Components.RenderFragment>, по соглашению необходимо присвоить имя `ChildContent`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-219">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="e1c27-220">`ParentComponent` в примере приложения может предоставить содержимое для отрисовки `ChildComponent`, заключив содержимое в теги `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-220">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="e1c27-221">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-221">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="e1c27-222">В связи с тем, как Blazor выполняет рендеринг дочернего содержимого, для компонентов рендеринга в цикле `for` требуется задать локальную переменную индекса, если в содержимом дочернего компонента используется переменная цикла приращения:</span><span class="sxs-lookup"><span data-stu-id="e1c27-222">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Param1="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="e1c27-223">Вместо этого можно использовать цикл `foreach` с <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="e1c27-223">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Param1="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="e1c27-224">Сплаттинг атрибутов и произвольные параметры</span><span class="sxs-lookup"><span data-stu-id="e1c27-224">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="e1c27-225">Компоненты могут записывать и визуализировать дополнительные атрибуты в дополнение к объявленным параметрам компонента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-225">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="e1c27-226">Можно записать дополнительные атрибуты в словарь, а затем выполнить *сплаттинг* для элемента при подготовке отрисовки компонента с помощью директивы [`@attributes`][3] Razor.</span><span class="sxs-lookup"><span data-stu-id="e1c27-226">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="e1c27-227">Этот сценарий полезен при определении компонента, который создает элемент разметки, поддерживающий разнообразные настройки.</span><span class="sxs-lookup"><span data-stu-id="e1c27-227">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="e1c27-228">Например, может оказаться утомительным по отдельности определять атрибуты для `<input>`, поддерживающего много параметров.</span><span class="sxs-lookup"><span data-stu-id="e1c27-228">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="e1c27-229">В следующем примере первый элемент `<input>` (`id="useIndividualParams"`) использует отдельные параметры компонента, а второй элемент `<input>` (`id="useAttributesDict"`) использует сплаттинг атрибутов:</span><span class="sxs-lookup"><span data-stu-id="e1c27-229">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

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

<span data-ttu-id="e1c27-230">Тип параметра должен реализовывать `IEnumerable<KeyValuePair<string, object>>` или `IReadOnlyDictionary<string, object>` с ключами строки.</span><span class="sxs-lookup"><span data-stu-id="e1c27-230">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="e1c27-231">При использовании обоих подходов получаются идентичные отрисованные элементы `<input>`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-231">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="e1c27-232">Чтобы принять произвольные атрибуты, определите параметр компонента с помощью атрибута [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) со свойством <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>, имеющим значение `true`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-232">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="e1c27-233">Свойство <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> в [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) позволяет параметру соответствовать всем атрибутам, которые не соответствуют никакому другому параметру.</span><span class="sxs-lookup"><span data-stu-id="e1c27-233">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="e1c27-234">Компонент может определять только один параметр с <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span><span class="sxs-lookup"><span data-stu-id="e1c27-234">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="e1c27-235">Тип свойства, используемый с <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>, должен быть назначаемым из `Dictionary<string, object>` с ключами строки.</span><span class="sxs-lookup"><span data-stu-id="e1c27-235">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="e1c27-236">В этом сценарии также можно использовать `IEnumerable<KeyValuePair<string, object>>` или `IReadOnlyDictionary<string, object>`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-236">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="e1c27-237">Расположение [`@attributes`][3] относительно положения атрибутов элемента имеет значение.</span><span class="sxs-lookup"><span data-stu-id="e1c27-237">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="e1c27-238">Когда выполняется сплаттинг [`@attributes`][3] для элемента, атрибуты обрабатываются справа налево (от последнего к первому).</span><span class="sxs-lookup"><span data-stu-id="e1c27-238">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="e1c27-239">Рассмотрим следующий пример компонента, использующего компонент `Child`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-239">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="e1c27-240">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-240">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="e1c27-241">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-241">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="e1c27-242">Атрибут `extra` компонента `Child` стоит справа от [`@attributes`][3].</span><span class="sxs-lookup"><span data-stu-id="e1c27-242">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="e1c27-243">`<div>`, визуализируемый компонентом `Parent`, содержит `extra="5"` при передаче через дополнительный атрибут, так как атрибуты обрабатываются справа налево (от последнего к первому):</span><span class="sxs-lookup"><span data-stu-id="e1c27-243">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="e1c27-244">В следующем примере порядок `extra` и [`@attributes`][3] в `<div>` компонента `Child` изменен на противоположный:</span><span class="sxs-lookup"><span data-stu-id="e1c27-244">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="e1c27-245">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-245">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="e1c27-246">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-246">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="e1c27-247">Визуализируемый `<div>` в компоненте `Parent` содержит `extra="10"` при передаче через дополнительный атрибут:</span><span class="sxs-lookup"><span data-stu-id="e1c27-247">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="e1c27-248">Запись ссылок на компоненты</span><span class="sxs-lookup"><span data-stu-id="e1c27-248">Capture references to components</span></span>

<span data-ttu-id="e1c27-249">Ссылки на компоненты позволяют ссылаться на экземпляр компонента, чтобы можно было выполнять команды для этого экземпляра, например `Show` или `Reset`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-249">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="e1c27-250">Чтобы записать ссылку на компонент, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="e1c27-250">To capture a component reference:</span></span>

* <span data-ttu-id="e1c27-251">Добавьте к дочернему компоненту атрибут [`@ref`][4].</span><span class="sxs-lookup"><span data-stu-id="e1c27-251">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="e1c27-252">Определите поле с тем же типом, что и у дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-252">Define a field with the same type as the child component.</span></span>

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="e1c27-253">При отрисовке компонента поле `loginDialog` заполняется экземпляром дочернего компонента `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-253">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="e1c27-254">Затем можно вызывать методы .NET в экземпляре компонента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-254">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e1c27-255">Переменная `loginDialog` заполняется только после отрисовки компонента, а ее выходные данные включают элемент `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-255">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="e1c27-256">Пока компонент не будет преобразован для просмотра, ссылка на него не используется.</span><span class="sxs-lookup"><span data-stu-id="e1c27-256">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="e1c27-257">Для управления ссылками на компоненты после завершения отрисовки компонента используйте [методы `OnAfterRenderAsync` или `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="e1c27-257">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="e1c27-258">Чтобы использовать ссылочную переменную с обработчиком событий, используйте лямбда-выражение или назначьте делегат обработчика событий в [методах `OnAfterRenderAsync` или `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="e1c27-258">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="e1c27-259">Это гарантирует, что ссылочная переменная будет назначена до назначения обработчика событий.</span><span class="sxs-lookup"><span data-stu-id="e1c27-259">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="e1c27-260">Сведения о ссылках на компоненты в цикле см. в разделе [Получение ссылок на несколько схожих дочерних компонентов (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="e1c27-260">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="e1c27-261">При записи ссылок на компоненты используется аналогичный синтаксис для [записи ссылок на элементы](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), это не функция взаимодействия JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e1c27-261">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="e1c27-262">Ссылки на компоненты не передаются в код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e1c27-262">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="e1c27-263">Они используются только в коде .NET.</span><span class="sxs-lookup"><span data-stu-id="e1c27-263">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="e1c27-264">**Не** используйте ссылки на компоненты для изменения состояния дочерних компонентов.</span><span class="sxs-lookup"><span data-stu-id="e1c27-264">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="e1c27-265">Вместо этого используйте обычные декларативные параметры для передачи данных дочерним компонентам.</span><span class="sxs-lookup"><span data-stu-id="e1c27-265">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="e1c27-266">Использование обычных декларативных параметров приводит к тому, что дочерние компоненты автоматически визуализируются в нужное время.</span><span class="sxs-lookup"><span data-stu-id="e1c27-266">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="e1c27-267">Контекст синхронизации</span><span class="sxs-lookup"><span data-stu-id="e1c27-267">Synchronization context</span></span>

<span data-ttu-id="e1c27-268">Blazor использует контекст синхронизации (<xref:System.Threading.SynchronizationContext>) для принудительного использования одного логического потока выполнения.</span><span class="sxs-lookup"><span data-stu-id="e1c27-268">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="e1c27-269">[Методы жизненного цикла ](xref:blazor/components/lifecycle) компонента и все обратные вызовы событий, сделанные Blazor, выполняются в этом контексте синхронизации.</span><span class="sxs-lookup"><span data-stu-id="e1c27-269">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="e1c27-270">Контекст синхронизации Blazor Server пытается эмулировать однопоточную среду таким образом, чтобы она точно соответствовала модели WebAssembly в браузере, которая является однопоточной.</span><span class="sxs-lookup"><span data-stu-id="e1c27-270">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="e1c27-271">В любой момент времени работа выполняется только в одном потоке, что создает впечатление единого логического потока.</span><span class="sxs-lookup"><span data-stu-id="e1c27-271">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="e1c27-272">Две операции не могут выполняться одновременно.</span><span class="sxs-lookup"><span data-stu-id="e1c27-272">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="e1c27-273">Избегайте блокирующих вызовов</span><span class="sxs-lookup"><span data-stu-id="e1c27-273">Avoid thread-blocking calls</span></span>

<span data-ttu-id="e1c27-274">Как правило, не следует вызывать следующие методы.</span><span class="sxs-lookup"><span data-stu-id="e1c27-274">Generally, don't call the following methods.</span></span> <span data-ttu-id="e1c27-275">Следующие методы блокируют поток и, таким образом, блокируют возобновление работы приложения до тех пор, пока не завершится базовый <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="e1c27-275">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="e1c27-276">Внешний вызов методов компонента для изменения состояния</span><span class="sxs-lookup"><span data-stu-id="e1c27-276">Invoke component methods externally to update state</span></span>

<span data-ttu-id="e1c27-277">Если компонент нужно изменить на основе внешнего события, такого как таймер или другие уведомления, используйте метод `InvokeAsync`, который выполняет отправку в контекст синхронизации Blazor.</span><span class="sxs-lookup"><span data-stu-id="e1c27-277">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="e1c27-278">Например, рассмотрим *службу уведомителя*, которая может уведомлять любой компонент, ожидающий передачи данных, об измененном состоянии:</span><span class="sxs-lookup"><span data-stu-id="e1c27-278">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="e1c27-279">Зарегистрируйте `NotifierService`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-279">Register the `NotifierService`:</span></span>

* <span data-ttu-id="e1c27-280">В Blazor WebAssembly зарегистрируйте службу как отдельную (singleton) в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-280">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="e1c27-281">В Blazor Server зарегистрируйте службу с заданной областью (scoped) в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-281">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="e1c27-282">Используйте `NotifierService` для изменения компонента:</span><span class="sxs-lookup"><span data-stu-id="e1c27-282">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="e1c27-283">В предыдущем примере `NotifierService` вызывает метод `OnNotify` компонента вне контекста синхронизации Blazor.</span><span class="sxs-lookup"><span data-stu-id="e1c27-283">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="e1c27-284">`InvokeAsync` используется для переключения на подходящий контекст и постановки отрисовки в очередь.</span><span class="sxs-lookup"><span data-stu-id="e1c27-284">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="e1c27-285">Использование \@key для управления сохранением элементов и компонентов</span><span class="sxs-lookup"><span data-stu-id="e1c27-285">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="e1c27-286">При отрисовке списка элементов или компонентов и последующем изменении элементов или компонентов алгоритм сравнения Blazor должен решить, какие из предыдущих элементов или компонентов можно оставить и как следует сопоставить с ними объекты модели.</span><span class="sxs-lookup"><span data-stu-id="e1c27-286">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="e1c27-287">Обычно этот процесс выполняется автоматически, и его можно игнорировать, но в некоторых случаях может потребоваться управлять данным процессом.</span><span class="sxs-lookup"><span data-stu-id="e1c27-287">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="e1c27-288">Рассмотрим следующий пример.</span><span class="sxs-lookup"><span data-stu-id="e1c27-288">Consider the following example:</span></span>

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

<span data-ttu-id="e1c27-289">Содержимое коллекции `People` может изменяться при вставке, удалении или повторном упорядочении записей.</span><span class="sxs-lookup"><span data-stu-id="e1c27-289">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="e1c27-290">Когда компонент отрисовывается повторно, компонент `<DetailsEditor>` может измениться на получение других значений параметра `Details`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-290">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="e1c27-291">Это может усложнить повторную отрисовку.</span><span class="sxs-lookup"><span data-stu-id="e1c27-291">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="e1c27-292">В некоторых случаях повторная отрисовка может привести к появлению заметных различий в поведении, таких как потеря фокуса элемента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-292">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="e1c27-293">Процесс сопоставления можно контролировать с помощью атрибута директивы [`@key`][5].</span><span class="sxs-lookup"><span data-stu-id="e1c27-293">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="e1c27-294">[`@key`][5] гарантирует, что алгоритм сравнения сохранит элементы или компоненты на основе значения ключа:</span><span class="sxs-lookup"><span data-stu-id="e1c27-294">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="e1c27-295">При изменении коллекции `People` алгоритм сравнения сохраняет связь между экземплярами `<DetailsEditor>` и экземплярами `person`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-295">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="e1c27-296">Если `Person` удаляется из списка `People`, то из пользовательского интерфейса удаляется только соответствующий экземпляр `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-296">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="e1c27-297">Другие экземпляры остаются без изменений.</span><span class="sxs-lookup"><span data-stu-id="e1c27-297">Other instances are left unchanged.</span></span>
* <span data-ttu-id="e1c27-298">Если в какой-либо позиции в списке вставляется `Person`, то в соответствующей позиции вставляется один новый экземпляр `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-298">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="e1c27-299">Другие экземпляры остаются без изменений.</span><span class="sxs-lookup"><span data-stu-id="e1c27-299">Other instances are left unchanged.</span></span>
* <span data-ttu-id="e1c27-300">При переупорядочении записей `Person` соответствующие экземпляры `<DetailsEditor>` сохраняются и переупорядочиваются в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="e1c27-300">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="e1c27-301">В некоторых сценариях использование [`@key`][5] уменьшает сложность повторной отрисовки и предотвращает потенциальные проблемы с изменяющимися элементами модели DOM с отслеживанием состояния, например положением фокуса.</span><span class="sxs-lookup"><span data-stu-id="e1c27-301">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e1c27-302">Ключи являются локальными для каждого компонента или элемента контейнера.</span><span class="sxs-lookup"><span data-stu-id="e1c27-302">Keys are local to each container element or component.</span></span> <span data-ttu-id="e1c27-303">Ключи не сравниваются глобально по всему документу.</span><span class="sxs-lookup"><span data-stu-id="e1c27-303">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="e1c27-304">Условия для использования \@key</span><span class="sxs-lookup"><span data-stu-id="e1c27-304">When to use \@key</span></span>

<span data-ttu-id="e1c27-305">Как правило, [`@key`][5] имеет смысл использовать при отрисовке списка (например, в блоке [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)) и при наличии подходящего значения для определения [`@key`][5].</span><span class="sxs-lookup"><span data-stu-id="e1c27-305">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="e1c27-306">Можно также использовать [`@key`][5], чтобы запретить Blazor сохранять поддерево элементов или компонентов при изменении объекта:</span><span class="sxs-lookup"><span data-stu-id="e1c27-306">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="e1c27-307">Если `@currentPerson` меняется, директива атрибута [`@key`][5] принуждает Blazor отменить весь блок элемента `<div>` с потомками и перестроить поддерево в пользовательском интерфейсе с использованием новых элементов и компонентов.</span><span class="sxs-lookup"><span data-stu-id="e1c27-307">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="e1c27-308">Это может быть полезно, если нужно гарантировать, что при изменении `@currentPerson` состояние пользовательского интерфейса не сохраняется.</span><span class="sxs-lookup"><span data-stu-id="e1c27-308">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="e1c27-309">Условия для отказа от использования \@key</span><span class="sxs-lookup"><span data-stu-id="e1c27-309">When not to use \@key</span></span>

<span data-ttu-id="e1c27-310">Сравнение с использованием [`@key`][5] подразумевает определенное снижение производительности.</span><span class="sxs-lookup"><span data-stu-id="e1c27-310">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="e1c27-311">Это снижение производительности незначительно, но указывать [`@key`][5] следует только в том случае, если управление правилами сохранения элементов или компонентов выгодно для приложения.</span><span class="sxs-lookup"><span data-stu-id="e1c27-311">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="e1c27-312">Даже если [`@key`][5] не используется, Blazor сохраняет экземпляры дочерних элементов и компонентов в максимально возможной степени.</span><span class="sxs-lookup"><span data-stu-id="e1c27-312">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="e1c27-313">Единственным преимуществом использования [`@key`][5] является контроль над тем, *как* экземпляры модели сопоставляются с сохраненными экземплярами компонентов, вместо выбора сопоставления с помощью алгоритма сравнения.</span><span class="sxs-lookup"><span data-stu-id="e1c27-313">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="e1c27-314">Значения, которые следует использовать для \@key</span><span class="sxs-lookup"><span data-stu-id="e1c27-314">What values to use for \@key</span></span>

<span data-ttu-id="e1c27-315">Как правило, для [`@key`][5] имеет смысл указать значение одного из следующих видов:</span><span class="sxs-lookup"><span data-stu-id="e1c27-315">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="e1c27-316">Экземпляры объектов модели (например, экземпляр `Person`, как в предыдущем примере).</span><span class="sxs-lookup"><span data-stu-id="e1c27-316">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="e1c27-317">Это гарантирует сохранение на основе равенства ссылок на объекты.</span><span class="sxs-lookup"><span data-stu-id="e1c27-317">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="e1c27-318">Уникальные идентификаторы (например, значения первичного ключа типа `int`, `string` или `Guid`).</span><span class="sxs-lookup"><span data-stu-id="e1c27-318">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="e1c27-319">Убедитесь, что значения, используемые для [`@key`][5], не конфликтуют.</span><span class="sxs-lookup"><span data-stu-id="e1c27-319">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="e1c27-320">Если в одном родительском элементе обнаруживаются конфликтующие значения, Blazor выдает исключение, поскольку не может детерминированно сопоставлять старые элементы или компоненты с новыми.</span><span class="sxs-lookup"><span data-stu-id="e1c27-320">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="e1c27-321">Используйте только уникальные значения, такие как экземпляры объекта или значения первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="e1c27-321">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="e1c27-322">Перезаписанные параметры</span><span class="sxs-lookup"><span data-stu-id="e1c27-322">Overwritten parameters</span></span>

<span data-ttu-id="e1c27-323">Предоставляются новые значения параметров, которые обычно переопределяют существующие, когда родительский компонент отрисовывается повторно.</span><span class="sxs-lookup"><span data-stu-id="e1c27-323">New parameter values are supplied, typically overwriting existing ones, when the parent component rerenders.</span></span>

<span data-ttu-id="e1c27-324">Рассмотрим следующий компонент `Expander`, который:</span><span class="sxs-lookup"><span data-stu-id="e1c27-324">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="e1c27-325">преобразует дочернее содержимое;</span><span class="sxs-lookup"><span data-stu-id="e1c27-325">Renders child content.</span></span>
* <span data-ttu-id="e1c27-326">переключает отображение дочернего содержимого с помощью параметра компонента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-326">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
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

<span data-ttu-id="e1c27-327">Компонент `Expander` добавляется в родительский компонент, который может вызывать <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span><span class="sxs-lookup"><span data-stu-id="e1c27-327">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="e1c27-328">Изначально компоненты `Expander` работают независимо, когда их свойства `Expanded` переключаются.</span><span class="sxs-lookup"><span data-stu-id="e1c27-328">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="e1c27-329">Дочерние компоненты сохраняют свои состояния, как и ожидалось.</span><span class="sxs-lookup"><span data-stu-id="e1c27-329">The child components maintain their states as expected.</span></span> <span data-ttu-id="e1c27-330">Когда в родительском элементе вызывается <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>, параметр `Expanded` первого дочернего компонента сбрасывается обратно к первоначальному значению (`true`).</span><span class="sxs-lookup"><span data-stu-id="e1c27-330">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="e1c27-331">Значение `Expanded` второго компонента `Expander` не сбрасывается, так как во втором компоненте не отображается дочернее содержимое.</span><span class="sxs-lookup"><span data-stu-id="e1c27-331">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="e1c27-332">Чтобы сохранить состояние в предыдущем сценарии, используйте *закрытое поле* в компоненте `Expander`, чтобы сохранить состояние переключения.</span><span class="sxs-lookup"><span data-stu-id="e1c27-332">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="e1c27-333">Следующий измененный компонент `Expander`:</span><span class="sxs-lookup"><span data-stu-id="e1c27-333">The following revised `Expander` component:</span></span>

* <span data-ttu-id="e1c27-334">Принимает значение параметра компонента `Expanded` из родительского элемента.</span><span class="sxs-lookup"><span data-stu-id="e1c27-334">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="e1c27-335">Присваивает значение параметра компонента *закрытому полю* (`expanded`) при [событии OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="e1c27-335">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="e1c27-336">Использует закрытое поле для поддержания внутреннего состояния переключения.</span><span class="sxs-lookup"><span data-stu-id="e1c27-336">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

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

## <a name="apply-an-attribute"></a><span data-ttu-id="e1c27-337">Применение атрибута</span><span class="sxs-lookup"><span data-stu-id="e1c27-337">Apply an attribute</span></span>

<span data-ttu-id="e1c27-338">Атрибуты можно применять к компонентам Razor с помощью директивы [`@attribute`][7].</span><span class="sxs-lookup"><span data-stu-id="e1c27-338">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="e1c27-339">В следующем примере атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) применяется к классу компонентов:</span><span class="sxs-lookup"><span data-stu-id="e1c27-339">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="e1c27-340">Условные атрибуты элемента HTML</span><span class="sxs-lookup"><span data-stu-id="e1c27-340">Conditional HTML element attributes</span></span>

<span data-ttu-id="e1c27-341">Атрибуты элемента HTML условно визуализируются в зависимости от значения .NET.</span><span class="sxs-lookup"><span data-stu-id="e1c27-341">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="e1c27-342">Если значение равно `false` или `null`, то атрибут не визуализируется.</span><span class="sxs-lookup"><span data-stu-id="e1c27-342">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="e1c27-343">Если значение равно `true`, атрибут визуализируется в свернутом виде.</span><span class="sxs-lookup"><span data-stu-id="e1c27-343">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="e1c27-344">В следующем примере `IsCompleted` определяет, визуализируется ли `checked` в разметке элемента:</span><span class="sxs-lookup"><span data-stu-id="e1c27-344">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="e1c27-345">Если `IsCompleted` имеет значение `true`, флажок визуализируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="e1c27-345">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="e1c27-346">Если `IsCompleted` имеет значение `false`, флажок визуализируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="e1c27-346">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="e1c27-347">Для получения дополнительной информации см. <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="e1c27-347">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="e1c27-348">Некоторые атрибуты HTML, такие как [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), работают неправильно, если типом .NET является `bool`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-348">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="e1c27-349">В этих случаях используйте тип `string` вместо `bool`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-349">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="e1c27-350">Необработанный HTML-код</span><span class="sxs-lookup"><span data-stu-id="e1c27-350">Raw HTML</span></span>

<span data-ttu-id="e1c27-351">Строки обычно визуализируются с помощью текстовых узлов модели DOM, что означает, что любая разметка, которую они могут содержать, игнорируется и обрабатывается как текстовый литерал.</span><span class="sxs-lookup"><span data-stu-id="e1c27-351">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="e1c27-352">Для отрисовки необработанного HTML-кода заключите HTML-содержимое в значение `MarkupString`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-352">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="e1c27-353">Это значение анализируется как HTML или SVG и вставляется в модель DOM.</span><span class="sxs-lookup"><span data-stu-id="e1c27-353">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="e1c27-354">Отрисовка необработанного HTML-кода, созданного из любого ненадежного источника, является **угрозой безопасности**, и ее следует избегать!</span><span class="sxs-lookup"><span data-stu-id="e1c27-354">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="e1c27-355">В следующем примере показано использование типа `MarkupString` для добавления блока статического HTML-содержимого в визуализируемые выходные данные компонента:</span><span class="sxs-lookup"><span data-stu-id="e1c27-355">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a><span data-ttu-id="e1c27-356">Шаблоны Razor</span><span class="sxs-lookup"><span data-stu-id="e1c27-356">Razor templates</span></span>

<span data-ttu-id="e1c27-357">Фрагменты отрисовки можно определить с помощью синтаксиса шаблонов Razor.</span><span class="sxs-lookup"><span data-stu-id="e1c27-357">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="e1c27-358">Шаблоны Razor позволяют определить фрагмент кода пользовательского интерфейса и подразумевают следующий формат.</span><span class="sxs-lookup"><span data-stu-id="e1c27-358">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="e1c27-359">В следующем примере показано, как указать значения <xref:Microsoft.AspNetCore.Components.RenderFragment> и <xref:Microsoft.AspNetCore.Components.RenderFragment%601> и визуализировать шаблоны непосредственно в компоненте.</span><span class="sxs-lookup"><span data-stu-id="e1c27-359">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="e1c27-360">Фрагменты отрисовки также могут передаваться в качестве аргументов в [шаблонные компоненты](xref:blazor/components/templated-components).</span><span class="sxs-lookup"><span data-stu-id="e1c27-360">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="e1c27-361">Визуализированные выходные данные предыдущего кода:</span><span class="sxs-lookup"><span data-stu-id="e1c27-361">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="e1c27-362">Статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="e1c27-362">Static assets</span></span>

<span data-ttu-id="e1c27-363">Blazor соответствует соглашению для приложений ASP.NET Core о размещении статических ресурсов в [папке `web root (wwwroot)`](xref:fundamentals/index#web-root) проекта.</span><span class="sxs-lookup"><span data-stu-id="e1c27-363">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="e1c27-364">Используйте базовый относительный путь (`/`) для ссылки на корневой веб-каталог статического ресурса.</span><span class="sxs-lookup"><span data-stu-id="e1c27-364">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="e1c27-365">В следующем примере `logo.png` физически находится в папке `{PROJECT ROOT}/wwwroot/images`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-365">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="e1c27-366">Компоненты Razor **не** поддерживают нотацию тильды с косой чертой (`~/`).</span><span class="sxs-lookup"><span data-stu-id="e1c27-366">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="e1c27-367">Сведения о настройке базового пути приложения см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="e1c27-367">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="e1c27-368">Вспомогательные функции тегов в компонентах не поддерживаются</span><span class="sxs-lookup"><span data-stu-id="e1c27-368">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="e1c27-369">[`Tag Helpers`Razor не поддерживаются в компонентах ](xref:mvc/views/tag-helpers/intro) (файлы с расширением `.razor`).</span><span class="sxs-lookup"><span data-stu-id="e1c27-369">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="e1c27-370">Чтобы обеспечить функциональные возможности, аналогичные вспомогательным функциям тегов, в Blazor, создайте компонент с теми же функциональными возможностями, что и вспомогательная функция тега, и используйте его вместо нее.</span><span class="sxs-lookup"><span data-stu-id="e1c27-370">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="e1c27-371">Изображения SVG</span><span class="sxs-lookup"><span data-stu-id="e1c27-371">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="e1c27-372">Так как Blazor выполняет рендеринг HTML-кода, поддерживаемые браузером изображения, включая изображения SVG (`.svg`), поддерживаются при использовании тега `<img>`.</span><span class="sxs-lookup"><span data-stu-id="e1c27-372">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="e1c27-373">Аналогичным образом изображения SVG поддерживаются в правилах CSS файла таблицы стилей (`.css`).</span><span class="sxs-lookup"><span data-stu-id="e1c27-373">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="e1c27-374">Однако встроенная разметка SVG не поддерживается во всех сценариях.</span><span class="sxs-lookup"><span data-stu-id="e1c27-374">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="e1c27-375">Если поместить тег `<svg>` непосредственно в файл компонента (`.razor`), базовая отрисовка изображений будет доступной, но многие расширенные сценарии пока не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="e1c27-375">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="e1c27-376">Например, теги `<use>` сейчас не учитываются, а с некоторыми тегами SVG невозможно использовать [`@bind`][10].</span><span class="sxs-lookup"><span data-stu-id="e1c27-376">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="e1c27-377">Дополнительные сведения см. в [справке по SVG в Blazor (dotnet/aspnetcore#18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="e1c27-377">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1c27-378">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e1c27-378">Additional resources</span></span>

* <span data-ttu-id="e1c27-379"><xref:blazor/security/server/threat-mitigation>. Содержит рекомендации по созданию приложений Blazor Server, которые должны соперничать в условиях нехватки ресурсов.</span><span class="sxs-lookup"><span data-stu-id="e1c27-379"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
