---
title: Просмотр компонентов в ASP.NET Core
author: rick-anderson
description: Узнайте, как компоненты представления используются в ASP.NET Core и как добавить их в приложения.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-components
ms.openlocfilehash: 8e97dc69ef167b5c08522c91691e0aded9f56908
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102930"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="4f797-103">Просмотр компонентов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f797-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="4f797-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="4f797-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4f797-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4f797-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="4f797-106">Компоненты представлений</span><span class="sxs-lookup"><span data-stu-id="4f797-106">View components</span></span>

<span data-ttu-id="4f797-107">Компоненты представлений похожи на частичные представления, но при этом гораздо функциональнее.</span><span class="sxs-lookup"><span data-stu-id="4f797-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="4f797-108">Компоненты представлений не используют привязку модели и зависят только от данных, предоставляемых при их вызове.</span><span class="sxs-lookup"><span data-stu-id="4f797-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="4f797-109">Эта статья написана с использованием контроллеров и представлений, но компоненты представлений также работают со Razor страницами.</span><span class="sxs-lookup"><span data-stu-id="4f797-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="4f797-110">Компонент представлений:</span><span class="sxs-lookup"><span data-stu-id="4f797-110">A view component:</span></span>

* <span data-ttu-id="4f797-111">Выполняет отрисовку фрагмента, а не всего отклика.</span><span class="sxs-lookup"><span data-stu-id="4f797-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="4f797-112">Включает те же преимущества разделения функций и тестирования, которые доступны между контроллером и представлением.</span><span class="sxs-lookup"><span data-stu-id="4f797-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="4f797-113">Может иметь параметры и бизнес-логику.</span><span class="sxs-lookup"><span data-stu-id="4f797-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="4f797-114">Обычно вызывается со страницы макета.</span><span class="sxs-lookup"><span data-stu-id="4f797-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="4f797-115">Компоненты представлений предназначены для случаев, когда имеется многоразовая логика отрисовки, которая слишком сложна для частичного представления, например:</span><span class="sxs-lookup"><span data-stu-id="4f797-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="4f797-116">Динамические меню навигации</span><span class="sxs-lookup"><span data-stu-id="4f797-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="4f797-117">Облако тегов (где выполняется запрос к базе данных)</span><span class="sxs-lookup"><span data-stu-id="4f797-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="4f797-118">Панель входа</span><span class="sxs-lookup"><span data-stu-id="4f797-118">Login panel</span></span>
* <span data-ttu-id="4f797-119">Корзина для покупок</span><span class="sxs-lookup"><span data-stu-id="4f797-119">Shopping cart</span></span>
* <span data-ttu-id="4f797-120">Недавно опубликованные статьи</span><span class="sxs-lookup"><span data-stu-id="4f797-120">Recently published articles</span></span>
* <span data-ttu-id="4f797-121">Содержимое боковой панели в типичном блоге</span><span class="sxs-lookup"><span data-stu-id="4f797-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="4f797-122">Панель входа, которая должна отображаться на каждой странице и содержит ссылки для выхода или входа в зависимости от состояния входа пользователя</span><span class="sxs-lookup"><span data-stu-id="4f797-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="4f797-123">Компонент представления состоит из двух частей: класса (обычно производного от [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) и возвращаемого результата (обычно это представление).</span><span class="sxs-lookup"><span data-stu-id="4f797-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="4f797-124">Как и контроллеры, компонент представления может быть объектом POCO, но большинству разработчиков потребуются преимущества методов и свойств, доступные при наследовании от `ViewComponent`.</span><span class="sxs-lookup"><span data-stu-id="4f797-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="4f797-125">При рассмотрении того, соответствуют ли компоненты представления спецификациям приложения, рекомендуется использовать Razor компоненты.</span><span class="sxs-lookup"><span data-stu-id="4f797-125">When considering if view components meet an app's specifications, consider using Razor Components instead.</span></span> Razor<span data-ttu-id="4f797-126">Компоненты также сочетают разметку с кодом C# для создания повторно используемых единиц пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="4f797-126"> Components also combine markup with C# code to produce reusable UI units.</span></span> Razor<span data-ttu-id="4f797-127">Компоненты предназначены для повышения производительности разработки при предоставлении логики пользовательского интерфейса и компоновки на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="4f797-127"> Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="4f797-128">Дополнительные сведения см. в разделе <xref:blazor/components/index>.</span><span class="sxs-lookup"><span data-stu-id="4f797-128">For more information, see <xref:blazor/components/index>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="4f797-129">Создание компонента представления</span><span class="sxs-lookup"><span data-stu-id="4f797-129">Creating a view component</span></span>

<span data-ttu-id="4f797-130">Этот раздел содержит общие требования к созданию компонента представления.</span><span class="sxs-lookup"><span data-stu-id="4f797-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="4f797-131">Далее в этой статье мы подробно рассмотрим каждый шаг и создадим компонент представления.</span><span class="sxs-lookup"><span data-stu-id="4f797-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="4f797-132">Класс компонентов представлений</span><span class="sxs-lookup"><span data-stu-id="4f797-132">The view component class</span></span>

<span data-ttu-id="4f797-133">Класс компонентов представлений можно создать одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="4f797-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="4f797-134">Наследование от *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="4f797-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="4f797-135">Декорирование класса с помощью атрибута `[ViewComponent]` или наследование от класса с помощью атрибута `[ViewComponent]`</span><span class="sxs-lookup"><span data-stu-id="4f797-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="4f797-136">Создание класса, где имя заканчивается суффиксом *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="4f797-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="4f797-137">Как и контроллеры, компоненты представлений должны быть открытыми, невложенными и неабстрактными классами.</span><span class="sxs-lookup"><span data-stu-id="4f797-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="4f797-138">Имя компонента представления — это имя класса с удаленным суффиксом "ViewComponent".</span><span class="sxs-lookup"><span data-stu-id="4f797-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="4f797-139">Его можно также можно задать явно с помощью свойства `ViewComponentAttribute.Name`.</span><span class="sxs-lookup"><span data-stu-id="4f797-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="4f797-140">Класс компонентов представлений:</span><span class="sxs-lookup"><span data-stu-id="4f797-140">A view component class:</span></span>

* <span data-ttu-id="4f797-141">Полностью поддерживает [внедрение зависимостей](../../fundamentals/dependency-injection.md) в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="4f797-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="4f797-142">Не участвует в жизненном цикле контроллера, поэтому в компоненте представления невозможно использовать [фильтры](../controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="4f797-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="4f797-143">Методы компонентов представлений</span><span class="sxs-lookup"><span data-stu-id="4f797-143">View component methods</span></span>

<span data-ttu-id="4f797-144">Компонент представления задает свою логику в методе `InvokeAsync`, возвращающем `Task<IViewComponentResult>`, или в синхронном методе `Invoke`, который возвращает `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="4f797-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="4f797-145">Параметры берутся непосредственно из вызова компонента представления, а не из привязки модели.</span><span class="sxs-lookup"><span data-stu-id="4f797-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="4f797-146">Компонент представления никогда не обрабатывает запрос напрямую.</span><span class="sxs-lookup"><span data-stu-id="4f797-146">A view component never directly handles a request.</span></span> <span data-ttu-id="4f797-147">Как правило, компонент представления инициализирует модель и передает ее в представление, вызвав метод `View`.</span><span class="sxs-lookup"><span data-stu-id="4f797-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="4f797-148">Если кратко, методы компонентов представлений:</span><span class="sxs-lookup"><span data-stu-id="4f797-148">In summary, view component methods:</span></span>

* <span data-ttu-id="4f797-149">Определяют метод `InvokeAsync`, который возвращает `Task<IViewComponentResult>`, или синхронный метод `Invoke`, возвращающий `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="4f797-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="4f797-150">Обычно инициализирует модель и передает ее в представление, вызывая `ViewComponent` `View` метод.</span><span class="sxs-lookup"><span data-stu-id="4f797-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="4f797-151">Получают параметры из вызывающего метода, а не HTTP.</span><span class="sxs-lookup"><span data-stu-id="4f797-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="4f797-152">Это связано с тем, что привязка модели отсутствует.</span><span class="sxs-lookup"><span data-stu-id="4f797-152">There's no model binding.</span></span>
* <span data-ttu-id="4f797-153">Недоступны непосредственно в качестве конечной точки HTTP.</span><span class="sxs-lookup"><span data-stu-id="4f797-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="4f797-154">Вызываются из кода (обычно в представлении).</span><span class="sxs-lookup"><span data-stu-id="4f797-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="4f797-155">Компонент представления никогда не обрабатывает запрос.</span><span class="sxs-lookup"><span data-stu-id="4f797-155">A view component never handles a request.</span></span>
* <span data-ttu-id="4f797-156">Перегружаются по сигнатуре, а не по сведениям из текущего HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="4f797-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="4f797-157">Путь поиска представления</span><span class="sxs-lookup"><span data-stu-id="4f797-157">View search path</span></span>

<span data-ttu-id="4f797-158">Среда выполнения ищет представление по следующим путям:</span><span class="sxs-lookup"><span data-stu-id="4f797-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="4f797-159">/Views/{Имя контроллера}/Components/{Имя компонента представления}/{Имя представления}</span><span class="sxs-lookup"><span data-stu-id="4f797-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="4f797-160">/Views/Shared/Components/{Имя компонента представления}/{Имя представления}</span><span class="sxs-lookup"><span data-stu-id="4f797-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="4f797-161">/Pages/Shared/Components/{Имя компонента представления}/{Имя представления}</span><span class="sxs-lookup"><span data-stu-id="4f797-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="4f797-162">Путь поиска применяется к проектам с использованием контроллеров и представлений и Razor страниц.</span><span class="sxs-lookup"><span data-stu-id="4f797-162">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="4f797-163">По умолчанию для компонента представления используется имя *Default*, то есть файл представления обычно называется *Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4f797-163">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="4f797-164">При создании результата компонента представления или при вызове метода `View` можно указать другое имя представления.</span><span class="sxs-lookup"><span data-stu-id="4f797-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="4f797-165">Мы рекомендуем назвать файл представления *Default.cshtml* и использовать путь *Views/Shared/Components/{Имя компонента представления}/{Имя представления}*.</span><span class="sxs-lookup"><span data-stu-id="4f797-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="4f797-166">Компонент представления `PriorityList` в этом примере использует представление компонента представления *Views/Shared/Components/PriorityList/Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4f797-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

### <a name="customize-the-view-search-path"></a><span data-ttu-id="4f797-167">Настройка пути поиска представления</span><span class="sxs-lookup"><span data-stu-id="4f797-167">Customize the view search path</span></span>

<span data-ttu-id="4f797-168">Чтобы настроить путь поиска представления, измените Razor <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> коллекцию.</span><span class="sxs-lookup"><span data-stu-id="4f797-168">To customize the view search path, modify Razor's <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> collection.</span></span> <span data-ttu-id="4f797-169">Например, чтобы найти представления в пути "/Components/{имя компонента представления}/{имя представления}", добавьте в коллекцию новый элемент:</span><span class="sxs-lookup"><span data-stu-id="4f797-169">For example, to search for views within the path "/Components/{View Component Name}/{View Name}", add a new item to the collection:</span></span>

[!code-cs[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

<span data-ttu-id="4f797-170">В приведенном выше коде заполнитель {0} представляет путь "Components/{имя компонента представления}/{имя представления}".</span><span class="sxs-lookup"><span data-stu-id="4f797-170">In the preceding code, the placeholder "{0}" represents the path "Components/{View Component Name}/{View Name}".</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="4f797-171">Вызов компонента представления</span><span class="sxs-lookup"><span data-stu-id="4f797-171">Invoking a view component</span></span>

<span data-ttu-id="4f797-172">Чтобы использовать компонент представления, вызовите следующий код внутри представления:</span><span class="sxs-lookup"><span data-stu-id="4f797-172">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="4f797-173">Эти параметры передаются в метод `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="4f797-173">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="4f797-174">Компонент представления `PriorityList`, разрабатываемый в рамках этой статьи, вызывается из файла представления *Views/ToDo/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4f797-174">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="4f797-175">Следующий код вызывает метод `InvokeAsync` с двумя параметрами:</span><span class="sxs-lookup"><span data-stu-id="4f797-175">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="4f797-176">Вызов компонента представления в качестве вспомогательной функции тегов</span><span class="sxs-lookup"><span data-stu-id="4f797-176">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="4f797-177">Для ASP.NET Core 1.1 и более поздних версий можно вызвать компонент представления в качестве [вспомогательной функции тегов](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="4f797-177">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="4f797-178">Параметры методов и классов, указанные в стиле Pascal, для вспомогательных функций тегов преобразуются в [кебаб-стиль](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="4f797-178">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="4f797-179">Вспомогательная функция тегов для вызова компонента представления использует элемент `<vc></vc>`.</span><span class="sxs-lookup"><span data-stu-id="4f797-179">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="4f797-180">Компонент представления задан следующим образом:</span><span class="sxs-lookup"><span data-stu-id="4f797-180">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="4f797-181">Чтобы использовать компонент представления в качестве вспомогательного приложения тегов, зарегистрируйте с помощью директивы `@addTagHelper`сборку, содержащую этот компонент представления.</span><span class="sxs-lookup"><span data-stu-id="4f797-181">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="4f797-182">Если компонент представления находится в сборке с именем `MyWebApp`, добавьте в файл *_ViewImports.cshtml* следующую директиву:</span><span class="sxs-lookup"><span data-stu-id="4f797-182">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="4f797-183">Зарегистрировать компонент представления в качестве вспомогательной функции тегов можно в любом файле, который ссылается на этот компонент представления.</span><span class="sxs-lookup"><span data-stu-id="4f797-183">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="4f797-184">Раздел [Управление областью вспомогательной функции тегов](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) содержит дополнительные сведения о том, как зарегистрировать вспомогательные функции тегов.</span><span class="sxs-lookup"><span data-stu-id="4f797-184">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="4f797-185">Метод `InvokeAsync`, используемый в этом учебнике:</span><span class="sxs-lookup"><span data-stu-id="4f797-185">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="4f797-186">В разметке вспомогательной функции тегов:</span><span class="sxs-lookup"><span data-stu-id="4f797-186">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="4f797-187">В предыдущем примере компонент представления `PriorityList` становится `priority-list`.</span><span class="sxs-lookup"><span data-stu-id="4f797-187">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="4f797-188">Параметры передаются в компонент представления как атрибуты в кебаб-стиле.</span><span class="sxs-lookup"><span data-stu-id="4f797-188">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="4f797-189">Вызов компонента представления непосредственно из контроллера</span><span class="sxs-lookup"><span data-stu-id="4f797-189">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="4f797-190">Компоненты представлений обычно вызываются из представления, но их можно вызывать непосредственно из метода контроллера.</span><span class="sxs-lookup"><span data-stu-id="4f797-190">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="4f797-191">Если компоненты представлений не определяют конечные точки, например контроллеры, можно легко реализовать действие контроллера, возвращающее содержимое `ViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="4f797-191">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="4f797-192">В этом примере компонент представления вызывается непосредственно из контроллера:</span><span class="sxs-lookup"><span data-stu-id="4f797-192">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="4f797-193">Пошаговое руководство. Создание простого компонента представления</span><span class="sxs-lookup"><span data-stu-id="4f797-193">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="4f797-194">[Скачайте](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) начальный код и выполните его сборку и тестирование.</span><span class="sxs-lookup"><span data-stu-id="4f797-194">[Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="4f797-195">Это простой проект с `ToDo` контроллером, отображающим список элементов *TODO* .</span><span class="sxs-lookup"><span data-stu-id="4f797-195">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![Список дел](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="4f797-197">Добавление класса ViewComponent</span><span class="sxs-lookup"><span data-stu-id="4f797-197">Add a ViewComponent class</span></span>

<span data-ttu-id="4f797-198">Создайте папку *ViewComponents* и добавьте следующий класс `PriorityListViewComponent`:</span><span class="sxs-lookup"><span data-stu-id="4f797-198">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="4f797-199">Примечания к коду:</span><span class="sxs-lookup"><span data-stu-id="4f797-199">Notes on the code:</span></span>

* <span data-ttu-id="4f797-200">Классы компонентов представлений могут находиться в **любой** папке проекта.</span><span class="sxs-lookup"><span data-stu-id="4f797-200">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="4f797-201">Так как имя класса PriorityList**ViewComponent** заканчивается суффиксом **ViewComponent**, среда выполнения использует строку "PriorityList" при ссылке на компонент класса из представления.</span><span class="sxs-lookup"><span data-stu-id="4f797-201">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="4f797-202">Я подробнее расскажу об этом чуть позже.</span><span class="sxs-lookup"><span data-stu-id="4f797-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="4f797-203">Атрибут `[ViewComponent]` может изменить имя, используемое для ссылки на компонент представления.</span><span class="sxs-lookup"><span data-stu-id="4f797-203">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="4f797-204">Например, мы могли назвать класс `XYZ` и применить атрибут `ViewComponent`:</span><span class="sxs-lookup"><span data-stu-id="4f797-204">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="4f797-205">Приведенный выше атрибут `[ViewComponent]` предписывает средству выбора компонентов представлений использовать имя `PriorityList` при поиске представлений, связанных с данным компонентом, и строку "PriorityList" при ссылке на компонент класса из представления.</span><span class="sxs-lookup"><span data-stu-id="4f797-205">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="4f797-206">Я подробнее расскажу об этом чуть позже.</span><span class="sxs-lookup"><span data-stu-id="4f797-206">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="4f797-207">Компонент использует [внедрение зависимостей](../../fundamentals/dependency-injection.md) для предоставления контекста данных.</span><span class="sxs-lookup"><span data-stu-id="4f797-207">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="4f797-208">`InvokeAsync` предоставляет метод, который возможно вызывать из представления и который может принять произвольное число аргументов.</span><span class="sxs-lookup"><span data-stu-id="4f797-208">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="4f797-209">Метод `InvokeAsync` возвращает набор элементов `ToDo`, удовлетворяющих параметрам `isDone` и `maxPriority`.</span><span class="sxs-lookup"><span data-stu-id="4f797-209">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="4f797-210">Создание представления компонента представления Razor</span><span class="sxs-lookup"><span data-stu-id="4f797-210">Create the view component Razor view</span></span>

* <span data-ttu-id="4f797-211">Создайте папку *Views/Shared/Components*.</span><span class="sxs-lookup"><span data-stu-id="4f797-211">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="4f797-212">Она \*\*должна \*\* называться *Components*.</span><span class="sxs-lookup"><span data-stu-id="4f797-212">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="4f797-213">Создайте папку *Views/Shared/Components/PriorityList*.</span><span class="sxs-lookup"><span data-stu-id="4f797-213">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="4f797-214">Ее имя должно соответствовать имени класса представлений компонентов или имени класса без суффикса (если мы следовали соглашению и использовали суффикс *ViewComponent* в имени класса).</span><span class="sxs-lookup"><span data-stu-id="4f797-214">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="4f797-215">Если вы использовали атрибут `ViewComponent`, имя класса должно соответствовать его обозначению.</span><span class="sxs-lookup"><span data-stu-id="4f797-215">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="4f797-216">Создайте представление views */Shared/Components/PriorityList/Default. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="4f797-216">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="4f797-217">RazorПредставление принимает список `TodoItem` и отображает их.</span><span class="sxs-lookup"><span data-stu-id="4f797-217">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="4f797-218">Если метод `InvokeAsync` компонента представления не передает имя представления (как в нашем примере), по соглашению используется имя *Default*.</span><span class="sxs-lookup"><span data-stu-id="4f797-218">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="4f797-219">Далее в этом учебнике я покажу, как передать имя представления.</span><span class="sxs-lookup"><span data-stu-id="4f797-219">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="4f797-220">Чтобы переопределить стиль по умолчанию для конкретного контроллера, добавьте представление в папку представления для конкретного контроллера (например, views */TODO/Components/PriorityList/Default. cshtml)*.</span><span class="sxs-lookup"><span data-stu-id="4f797-220">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span></span>

    <span data-ttu-id="4f797-221">Если компонент представления связан с конкретным контроллером, его можно добавить в папку этого контроллера (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="4f797-221">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="4f797-222">Добавьте объект, `div` содержащий вызов компонента списка приоритетов, в нижнюю часть файла *views/TODO/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4f797-222">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="4f797-223">`@await Component.InvokeAsync` в разметке показывает синтаксис для вызова компонентов представлений.</span><span class="sxs-lookup"><span data-stu-id="4f797-223">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="4f797-224">Первым аргументом является имя компонента, который требуется вызвать.</span><span class="sxs-lookup"><span data-stu-id="4f797-224">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="4f797-225">Последующие параметры передаются в компонент.</span><span class="sxs-lookup"><span data-stu-id="4f797-225">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="4f797-226">`InvokeAsync` может занять произвольное число аргументов.</span><span class="sxs-lookup"><span data-stu-id="4f797-226">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="4f797-227">Проверьте работу приложения.</span><span class="sxs-lookup"><span data-stu-id="4f797-227">Test the app.</span></span> <span data-ttu-id="4f797-228">Следующий рисунок показывает список дел и элементы с приоритетом:</span><span class="sxs-lookup"><span data-stu-id="4f797-228">The following image shows the ToDo list and the priority items:</span></span>

![список дел и элементы с приоритетом](view-components/_static/pi.png)

<span data-ttu-id="4f797-230">Кроме того, компонент представления можно вызвать непосредственно из контроллера:</span><span class="sxs-lookup"><span data-stu-id="4f797-230">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![элементы с приоритетом из действия IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="4f797-232">Указание имени представления</span><span class="sxs-lookup"><span data-stu-id="4f797-232">Specifying a view name</span></span>

<span data-ttu-id="4f797-233">Сложному представлению компонента в некоторых условиях может потребоваться указать нестандартное представление.</span><span class="sxs-lookup"><span data-stu-id="4f797-233">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="4f797-234">Ниже показано, как указать представление "PVC" из метода `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="4f797-234">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="4f797-235">Обновите метод `InvokeAsync` в классе `PriorityListViewComponent`.</span><span class="sxs-lookup"><span data-stu-id="4f797-235">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="4f797-236">Скопируйте файл *Views/Shared/Components/PriorityList/Default.cshtml* в представление *Views/Shared/Components/PriorityList/PVC.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4f797-236">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="4f797-237">Добавьте заголовок, чтобы указать используемое представление PVC.</span><span class="sxs-lookup"><span data-stu-id="4f797-237">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="4f797-238">Измените *Views/ToDo/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4f797-238">Update *Views/ToDo/Index.cshtml*:</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="4f797-239">Запустите приложение и проверьте представление PVC.</span><span class="sxs-lookup"><span data-stu-id="4f797-239">Run the app and verify PVC view.</span></span>

![Компонент представления с приоритетом](view-components/_static/pvc.png)

<span data-ttu-id="4f797-241">Если представление PVC не отображается, убедитесь, что вы вызываете компонент представления с приоритетом 4 или выше.</span><span class="sxs-lookup"><span data-stu-id="4f797-241">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="4f797-242">Проверка пути к представлению</span><span class="sxs-lookup"><span data-stu-id="4f797-242">Examine the view path</span></span>

* <span data-ttu-id="4f797-243">Задайте для параметра приоритета значение 3 или меньше, чтобы представление с приоритетом не возвращалось.</span><span class="sxs-lookup"><span data-stu-id="4f797-243">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="4f797-244">Временно переименуйте *Views/ToDo/Components/PriorityList/Default.cshtml* в *1Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4f797-244">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="4f797-245">Проверьте приложение, при этом происходит следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="4f797-245">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="4f797-246">Скопируйте *views/TODO/Components/PriorityList/1Default. cshtml* в *views/Shared/Components/PriorityList/Default. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4f797-246">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="4f797-247">Добавьте разметку в представление компонента представления дел *Shared*, чтобы указать, что это представление из папки *Shared*.</span><span class="sxs-lookup"><span data-stu-id="4f797-247">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="4f797-248">Протестируйте представление компонента **Shared**.</span><span class="sxs-lookup"><span data-stu-id="4f797-248">Test the **Shared** component view.</span></span>

![Выходные данные дел с представлением компонента Shared](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="4f797-250">Как избежать жестко запрограммированных строк</span><span class="sxs-lookup"><span data-stu-id="4f797-250">Avoiding hard-coded strings</span></span>

<span data-ttu-id="4f797-251">Чтобы обеспечить безопасность во время компиляции, можно заменить жестко заданное имя компонента представления на имя класса.</span><span class="sxs-lookup"><span data-stu-id="4f797-251">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="4f797-252">Создайте компонент представления без суффикса "ViewComponent":</span><span class="sxs-lookup"><span data-stu-id="4f797-252">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="4f797-253">Добавьте `using` оператор в Razor файл представления и используйте `nameof` оператор:</span><span class="sxs-lookup"><span data-stu-id="4f797-253">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="4f797-254">Выполнение синхронных задач</span><span class="sxs-lookup"><span data-stu-id="4f797-254">Perform synchronous work</span></span>

<span data-ttu-id="4f797-255">Платформа обрабатывает вызов синхронного метода `Invoke`, если не требуется асинхронное выполнение работы.</span><span class="sxs-lookup"><span data-stu-id="4f797-255">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="4f797-256">Следующий метод создает синхронный компонент представления `Invoke`:</span><span class="sxs-lookup"><span data-stu-id="4f797-256">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="4f797-257">В файле компонента представления Razor перечислены строки, передаваемые `Invoke` методу (*Views/Home/Components/PriorityList/Default. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="4f797-257">The view component's Razor file lists the strings passed to the `Invoke` method (*Views/Home/Components/PriorityList/Default.cshtml*):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="4f797-258">Компонент представления вызывается в Razor файле (например, *Views/Home/Index. cshtml*) с помощью одного из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="4f797-258">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="4f797-259">Вспомогательное приложение тегов</span><span class="sxs-lookup"><span data-stu-id="4f797-259">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="4f797-260">Чтобы использовать подход <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>, вызовите `Component.InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="4f797-260">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="4f797-261">Компонент представления вызывается в Razor файле (например, *Views/Home/Index. cshtml*) с <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> .</span><span class="sxs-lookup"><span data-stu-id="4f797-261">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="4f797-262">Вызов `Component.InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="4f797-262">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="4f797-263">Чтобы использовать вспомогательное приложение тэгов зарегистрируйте с помощью директивы `@addTagHelper`сборку, содержащую этот компонент представления (он находится в сборке с именем `MyWebApp`):</span><span class="sxs-lookup"><span data-stu-id="4f797-263">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="4f797-264">Используйте вспомогательную функцию тега компонента View в Razor файле разметки:</span><span class="sxs-lookup"><span data-stu-id="4f797-264">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="4f797-265">Сигнатура метода `PriorityList.Invoke` синхронна, но Razor находит и вызывает метод `Component.InvokeAsync` в файле разметки.</span><span class="sxs-lookup"><span data-stu-id="4f797-265">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="4f797-266">Все параметры компонентов представления обязательны</span><span class="sxs-lookup"><span data-stu-id="4f797-266">All view component parameters are required</span></span>

<span data-ttu-id="4f797-267">Каждый параметр в компоненте представления является обязательным атрибутом.</span><span class="sxs-lookup"><span data-stu-id="4f797-267">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="4f797-268">Также см. [эту проблему в GitHub](https://github.com/dotnet/AspNetCore/issues/5011).</span><span class="sxs-lookup"><span data-stu-id="4f797-268">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="4f797-269">Если какой-либо параметр отсутствует:</span><span class="sxs-lookup"><span data-stu-id="4f797-269">If any  parameter is omitted:</span></span>

* <span data-ttu-id="4f797-270">Сигнатура метода `InvokeAsync` не совпадет, поэтому метод не будет выполняться.</span><span class="sxs-lookup"><span data-stu-id="4f797-270">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="4f797-271">Компонент ViewComponent не отобразит никакой разметки.</span><span class="sxs-lookup"><span data-stu-id="4f797-271">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="4f797-272">Не будут выдаваться ошибки.</span><span class="sxs-lookup"><span data-stu-id="4f797-272">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f797-273">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="4f797-273">Additional resources</span></span>

* [<span data-ttu-id="4f797-274">Внедрение зависимостей в представления</span><span class="sxs-lookup"><span data-stu-id="4f797-274">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
