---
title: Макет в ASP.NET Core
author: ardalis
description: Узнайте, как использовать общие макеты, директивы и как выполнять общий код перед преобразованием представлений для просмотра в приложении ASP.NET Core.
ms.author: riande
ms.date: 07/30/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/layout
ms.openlocfilehash: 502df268e7f5f33acfffccd5ec0bd65267fa12da
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060980"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="58836-103">Макет в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58836-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="58836-104">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith) и [Дейв Брок](https://twitter.com/daveabrock) (Dave Brock)</span><span class="sxs-lookup"><span data-stu-id="58836-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="58836-105">На страницах и в представлениях часто есть общие визуальные и программные элементы.</span><span class="sxs-lookup"><span data-stu-id="58836-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="58836-106">В этой статье демонстрируются следующие возможности.</span><span class="sxs-lookup"><span data-stu-id="58836-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="58836-107">Использование общих макетов.</span><span class="sxs-lookup"><span data-stu-id="58836-107">Use common layouts.</span></span>
* <span data-ttu-id="58836-108">Совместное использование директив.</span><span class="sxs-lookup"><span data-stu-id="58836-108">Share directives.</span></span>
* <span data-ttu-id="58836-109">Запуск общего кода до отрисовки страниц или представлений.</span><span class="sxs-lookup"><span data-stu-id="58836-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="58836-110">В этом документе рассматриваются два разных подхода к ASP.NET Core MVC: Razor страницы и контроллеры с представлениями.</span><span class="sxs-lookup"><span data-stu-id="58836-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: Razor Pages and controllers with views.</span></span> <span data-ttu-id="58836-111">С этой точки зрения различия минимальны:</span><span class="sxs-lookup"><span data-stu-id="58836-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="58836-112">Razor Страницы находятся в папке *pages* .</span><span class="sxs-lookup"><span data-stu-id="58836-112">Razor Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="58836-113">Контроллеры с представлениями используют папку *Views* для представлений.</span><span class="sxs-lookup"><span data-stu-id="58836-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="58836-114">Что такое макет</span><span class="sxs-lookup"><span data-stu-id="58836-114">What is a Layout</span></span>

<span data-ttu-id="58836-115">Большинство веб-приложений имеют общий макет, который обеспечивает согласованный пользовательский интерфейс при переходе между страницами.</span><span class="sxs-lookup"><span data-stu-id="58836-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="58836-116">Макет, как правило, включает в себя общие элементы пользовательского интерфейса, такие как верхний и нижний колонтитулы, а также элементы навигации или меню.</span><span class="sxs-lookup"><span data-stu-id="58836-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![Пример макета страницы](layout/_static/page-layout.png)

<span data-ttu-id="58836-118">Структуры HTML, такие как скрипты и таблицы стилей, также часто используются совместно несколькими страницами в приложении.</span><span class="sxs-lookup"><span data-stu-id="58836-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="58836-119">Все эти общие элементы могут быть определены в файле *макета* , на который затем может ссылаться любое представление, используемое в приложении.</span><span class="sxs-lookup"><span data-stu-id="58836-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="58836-120">Макеты позволяют сократить дублирование кода в представлениях.</span><span class="sxs-lookup"><span data-stu-id="58836-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="58836-121">В соответствии с соглашением макет по умолчанию для приложения ASP.NET Core имеет имя *_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="58836-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml* .</span></span> <span data-ttu-id="58836-122">В новых проектах ASP.NET Core, созданных с применением шаблонов, используются следующие файлы макета.</span><span class="sxs-lookup"><span data-stu-id="58836-122">The layout files for new ASP.NET Core projects created with the templates are:</span></span>

* <span data-ttu-id="58836-123">Razor Страницы: *pages/Shared/_layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="58836-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![Папка Pages в обозревателе решений](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="58836-125">Контроллер с представлениями: папка *Views/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58836-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

  ![Папка Views в обозревателе решений](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="58836-127">Макет определяет шаблон верхнего уровня для представлений в приложении.</span><span class="sxs-lookup"><span data-stu-id="58836-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="58836-128">Для приложений макет не требуется.</span><span class="sxs-lookup"><span data-stu-id="58836-128">Apps don't require a layout.</span></span> <span data-ttu-id="58836-129">В приложении может быть определено несколько макетов для разных представлений.</span><span class="sxs-lookup"><span data-stu-id="58836-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="58836-130">В следующем коде показан файл макета для проекта, созданного по шаблону, с контроллером и представлениями:</span><span class="sxs-lookup"><span data-stu-id="58836-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="58836-131">Указание макета</span><span class="sxs-lookup"><span data-stu-id="58836-131">Specifying a Layout</span></span>

<span data-ttu-id="58836-132">Razor представления имеют `Layout` свойство.</span><span class="sxs-lookup"><span data-stu-id="58836-132">Razor views have a `Layout` property.</span></span> <span data-ttu-id="58836-133">С его помощью указывается макет в отдельных представлениях:</span><span class="sxs-lookup"><span data-stu-id="58836-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="58836-134">Указанный макет может использовать полный путь (например, */Pages/Shared/_Layout.cshtml* или */Views/Shared/_Layout.cshtml* ) или частичное имя (например, `_Layout`).</span><span class="sxs-lookup"><span data-stu-id="58836-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml* ) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="58836-135">Если указано частичное имя, Razor обработчик представлений выполняет поиск файла макета с помощью стандартного процесса обнаружения.</span><span class="sxs-lookup"><span data-stu-id="58836-135">When a partial name is provided, the Razor view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="58836-136">Сначала поиск выполняется в папке, где существует метод обработчика (или контроллер), а затем в папке *Shared* .</span><span class="sxs-lookup"><span data-stu-id="58836-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="58836-137">Процесс обнаружения аналогичен тому, который применяется для поиска [частичных представлений](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="58836-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="58836-138">По умолчанию каждый макет должен вызывать метод `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="58836-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="58836-139">При каждом вызове `RenderBody` содержимое представления будет преобразовываться для просмотра.</span><span class="sxs-lookup"><span data-stu-id="58836-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a><span data-ttu-id="58836-140">Разделы</span><span class="sxs-lookup"><span data-stu-id="58836-140">Sections</span></span>

<span data-ttu-id="58836-141">Макет может при необходимости ссылаться на один или несколько *разделов* , вызывая метод `RenderSection`.</span><span class="sxs-lookup"><span data-stu-id="58836-141">A layout can optionally reference one or more *sections* , by calling `RenderSection`.</span></span> <span data-ttu-id="58836-142">Разделы — это средство для упорядочения размещения определенных элементов на странице.</span><span class="sxs-lookup"><span data-stu-id="58836-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="58836-143">В каждом вызове `RenderSection` можно указывать, является ли раздел обязательным или необязательным:</span><span class="sxs-lookup"><span data-stu-id="58836-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

<span data-ttu-id="58836-144">Если обязательный раздел не найден, создается исключение.</span><span class="sxs-lookup"><span data-stu-id="58836-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="58836-145">Отдельные представления задают содержимое, которое должно быть отображено в разделе с использованием `@section` Razor синтаксиса.</span><span class="sxs-lookup"><span data-stu-id="58836-145">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="58836-146">Если на странице или в представлении определяется раздел, он должен быть преобразован для просмотра (в противном случае произойдет ошибка).</span><span class="sxs-lookup"><span data-stu-id="58836-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="58836-147">Пример `@section` определения в Razor представлении страниц:</span><span class="sxs-lookup"><span data-stu-id="58836-147">An example `@section` definition in Razor Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

<span data-ttu-id="58836-148">В приведенном выше коде *scripts/main.js* добавляется в раздел `scripts` на странице или в представлении.</span><span class="sxs-lookup"><span data-stu-id="58836-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="58836-149">Другие страницы или представления в одном приложении могут не требовать этот скрипт и не определять раздел скриптов.</span><span class="sxs-lookup"><span data-stu-id="58836-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="58836-150">Следующая разметка использует [вспомогательную функция тега частичного представления](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) для подготовки к просмотру *_ValidationScriptsPartial.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58836-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml* :</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="58836-151">Предыдущая разметка была сформирована [формированием шаблонов Identity ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="58836-151">The preceding markup was generated by [scaffolding Identity](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="58836-152">Разделы, определенные на странице или в представлении, доступны только непосредственно на странице макета.</span><span class="sxs-lookup"><span data-stu-id="58836-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="58836-153">На них нельзя ссылаться из частичных представлений, компонентов представлений или других частей системы представлений.</span><span class="sxs-lookup"><span data-stu-id="58836-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="58836-154">Пропуск разделов</span><span class="sxs-lookup"><span data-stu-id="58836-154">Ignoring sections</span></span>

<span data-ttu-id="58836-155">По умолчанию тело и все разделы страницы содержимого должны преобразовываться для просмотра страницей макета.</span><span class="sxs-lookup"><span data-stu-id="58836-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="58836-156">RazorОбработчик представлений применяет это, отслеживая отображение текста и каждого раздела.</span><span class="sxs-lookup"><span data-stu-id="58836-156">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="58836-157">Чтобы подсистема представлений пропустила тело или разделы, вызовите методы `IgnoreBody` и `IgnoreSection`.</span><span class="sxs-lookup"><span data-stu-id="58836-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="58836-158">Текст и все разделы Razor страницы должны быть либо визуализированы, либо игнорироваться.</span><span class="sxs-lookup"><span data-stu-id="58836-158">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="58836-159">Импорт общих директив</span><span class="sxs-lookup"><span data-stu-id="58836-159">Importing Shared Directives</span></span>

<span data-ttu-id="58836-160">Представления и страницы могут использовать Razor директивы для импорта пространств имен и использования [внедрения зависимостей](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="58836-160">Views and pages can use Razor directives to import namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="58836-161">Директивы, используемые несколькими представлениями, можно указать в общем файле *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="58836-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="58836-162">Файл `_ViewImports` поддерживает следующие директивы:</span><span class="sxs-lookup"><span data-stu-id="58836-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="58836-163">Файл не поддерживает другие Razor функции, такие как функции и определения разделов.</span><span class="sxs-lookup"><span data-stu-id="58836-163">The file doesn't support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="58836-164">Пример файла `_ViewImports.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="58836-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="58836-165">Файл *_ViewImports.cshtml* для приложения ASP.NET Core MVC обычно находится в папке *Pages* (или *Views* ).</span><span class="sxs-lookup"><span data-stu-id="58836-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views* ) folder.</span></span> <span data-ttu-id="58836-166">Файл *_ViewImports.cshtml* можно поместить в любую папку, но в этом случае он будет применяться только к страницам или представлениям в этой папке и вложенных в нее папках.</span><span class="sxs-lookup"><span data-stu-id="58836-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="58836-167">Файлы `_ViewImports` обрабатываются начиная с корневого уровня, а затем для каждой папки вплоть до расположения самой страницы или представления.</span><span class="sxs-lookup"><span data-stu-id="58836-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="58836-168">Параметры `_ViewImports`, заданные на корневом уровне, можно переопределить на уровне папки.</span><span class="sxs-lookup"><span data-stu-id="58836-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="58836-169">Например, предположим, что:</span><span class="sxs-lookup"><span data-stu-id="58836-169">For example, suppose:</span></span>

* <span data-ttu-id="58836-170">Файл корневого уровня *_ViewImports.cshtml* включает `@model MyModel1` и `@addTagHelper *, MyTagHelper1`.</span><span class="sxs-lookup"><span data-stu-id="58836-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="58836-171">Файл вложенной папки *_ViewImports.cshtml* включает `@model MyModel2` и `@addTagHelper *, MyTagHelper2`.</span><span class="sxs-lookup"><span data-stu-id="58836-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="58836-172">Страницы и представления во вложенной папке будут иметь доступ к вспомогательным функциям тегов и модели `MyModel2`.</span><span class="sxs-lookup"><span data-stu-id="58836-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="58836-173">Если в иерархии файлов найдено несколько файлов *_ViewImports.cshtml* , директивы ведут себя следующим образом:</span><span class="sxs-lookup"><span data-stu-id="58836-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="58836-174">`@addTagHelper`, `@removeTagHelper`: выполняются все директивы по порядку;</span><span class="sxs-lookup"><span data-stu-id="58836-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="58836-175">`@tagHelperPrefix`: ближайшая к представлению директива переопределяет все остальные;</span><span class="sxs-lookup"><span data-stu-id="58836-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="58836-176">`@model`: ближайшая к представлению директива переопределяет все остальные;</span><span class="sxs-lookup"><span data-stu-id="58836-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="58836-177">`@inherits`: ближайшая к представлению директива переопределяет все остальные;</span><span class="sxs-lookup"><span data-stu-id="58836-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="58836-178">`@using`: включаются все директивы, повторяющиеся пропускаются;</span><span class="sxs-lookup"><span data-stu-id="58836-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="58836-179">`@inject`: для каждого свойства ближайшая к представлению директива переопределяет все остальные директивы с тем же именем свойства.</span><span class="sxs-lookup"><span data-stu-id="58836-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="58836-180">Выполнение кода перед каждым представлением</span><span class="sxs-lookup"><span data-stu-id="58836-180">Running Code Before Each View</span></span>

<span data-ttu-id="58836-181">Код, который должен быть запущен перед каждым представлением или страницей, нужно поместить в файл *_ViewStart.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="58836-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="58836-182">По соглашению файл *_ViewStart.cshtml* находится в папке *Pages* (или *Views* ).</span><span class="sxs-lookup"><span data-stu-id="58836-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views* ) folder.</span></span> <span data-ttu-id="58836-183">Операторы, перечисленные в файле *_ViewStart.cshtml* , выполняются перед каждым полным представлением (но не перед макетами и не перед частичными представлениями).</span><span class="sxs-lookup"><span data-stu-id="58836-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="58836-184">Так же как файлы [ViewImports.cshtml](xref:mvc/views/layout#viewimports), файлы *_ViewStart.cshtml* являются иерархическими.</span><span class="sxs-lookup"><span data-stu-id="58836-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="58836-185">Если файл *_ViewStart.cshtml* определен в папке представлений или страниц, он будет применяться после определенного в корне папки *Pages* (или *Views* ) (при его наличии).</span><span class="sxs-lookup"><span data-stu-id="58836-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views* ) folder (if any).</span></span>

<span data-ttu-id="58836-186">Пример файла *_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58836-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="58836-187">Приведенный файл предписывает всем представлениям использовать макет *_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="58836-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="58836-188">*_ViewStart.cshtml* и *_ViewImports.cshtml* обычно **не** помещаются в папку */Pages/Shared* (или */Views/Shared* ).</span><span class="sxs-lookup"><span data-stu-id="58836-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared* ) folder.</span></span> <span data-ttu-id="58836-189">Версии этих файлов, которые должны действовать на уровне приложения, следует помещать непосредственно в папку */Pages* (или */Views* ).</span><span class="sxs-lookup"><span data-stu-id="58836-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views* ) folder.</span></span>
