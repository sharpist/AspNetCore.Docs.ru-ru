---
title: Вспомогательная функция тега компонента в ASP.NET Core
author: guardrex
ms.author: riande
description: Узнайте, как использовать вспомогательную функцию тега компонента ASP.NET Core для отрисовки Razor компонентов на страницах и в представлениях.
ms.custom: mvc
ms.date: 04/15/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 1a0422da6bd48049cac73debe7d335da91e311be
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633920"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="344f6-103">Вспомогательная функция тега компонента в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="344f6-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="344f6-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="344f6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="344f6-105">Чтобы отрисовать компонент из страницы или представления, используйте [вспомогательную функцию тега компонента](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="344f6-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="344f6-106">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="344f6-106">Prerequisites</span></span>

<span data-ttu-id="344f6-107">Следуйте указаниям в разделе *Подготовка приложения к использованию компонентов в страницах и представлениях* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> статьи.</span><span class="sxs-lookup"><span data-stu-id="344f6-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="344f6-108">Вспомогательная функция тега компонента</span><span class="sxs-lookup"><span data-stu-id="344f6-108">Component Tag Helper</span></span>

<span data-ttu-id="344f6-109">Следующая вспомогательная функция тега Component визуализирует `Counter` компонент на странице или в представлении:</span><span class="sxs-lookup"><span data-stu-id="344f6-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="344f6-110">В предыдущем примере предполагается, что `Counter` компонент находится в папке *pages* приложения.</span><span class="sxs-lookup"><span data-stu-id="344f6-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="344f6-111">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `@using BlazorSample.Pages`).</span><span class="sxs-lookup"><span data-stu-id="344f6-111">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages`).</span></span>

<span data-ttu-id="344f6-112">Вспомогательная функция тега компонента также может передавать параметры в компоненты.</span><span class="sxs-lookup"><span data-stu-id="344f6-112">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="344f6-113">Рассмотрим следующий `ColorfulCheckbox` компонент, устанавливающий цвет и размер метки флажка:</span><span class="sxs-lookup"><span data-stu-id="344f6-113">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="344f6-114">`Size` `int` Параметры компонента () `Color` и `string` ( [component parameters](xref:blazor/components/index#component-parameters) ) могут быть заданы вспомогательной функцией тега компонента:</span><span class="sxs-lookup"><span data-stu-id="344f6-114">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="344f6-115">В предыдущем примере предполагается, что `ColorfulCheckbox` компонент находится в *общей* папке приложения.</span><span class="sxs-lookup"><span data-stu-id="344f6-115">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="344f6-116">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `@using BlazorSample.Shared`).</span><span class="sxs-lookup"><span data-stu-id="344f6-116">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="344f6-117">Следующий код HTML отображается на странице или в представлении:</span><span class="sxs-lookup"><span data-stu-id="344f6-117">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="344f6-118">Для передачи строки в кавычках требуется [явное Razor выражение](xref:mvc/views/razor#explicit-razor-expressions), как показано `param-Color` в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="344f6-118">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="344f6-119">RazorПоведение синтаксического анализа для `string` значения типа не применяется к `param-*` атрибуту, так как атрибут является `object` типом.</span><span class="sxs-lookup"><span data-stu-id="344f6-119">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="344f6-120">Тип параметра должен быть сериализуемым JSON, что обычно означает, что тип должен иметь конструктор по умолчанию и устанавливаемые свойства.</span><span class="sxs-lookup"><span data-stu-id="344f6-120">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="344f6-121">Например, можно указать значение для `Size` и `Color` в предыдущем примере, так как типы `Size` и `Color` являются примитивными типами ( `int` и `string` ), которые поддерживаются сериализатором JSON.</span><span class="sxs-lookup"><span data-stu-id="344f6-121">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="344f6-122">В следующем примере объект класса передается в компонент:</span><span class="sxs-lookup"><span data-stu-id="344f6-122">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="344f6-123">*MyClass.CS*:</span><span class="sxs-lookup"><span data-stu-id="344f6-123">*MyClass.cs*:</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="344f6-124">**Класс должен иметь открытый конструктор без параметров.**</span><span class="sxs-lookup"><span data-stu-id="344f6-124">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="344f6-125">*Shared/MyComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="344f6-125">*Shared/MyComponent.razor*:</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="344f6-126">*Pages/MyPage. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="344f6-126">*Pages/MyPage.cshtml*:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="344f6-127">В предыдущем примере предполагается, что `MyComponent` компонент находится в *общей* папке приложения.</span><span class="sxs-lookup"><span data-stu-id="344f6-127">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="344f6-128">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `@using BlazorSample` и `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="344f6-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="344f6-129">`MyClass` находится в пространстве имен приложения.</span><span class="sxs-lookup"><span data-stu-id="344f6-129">`MyClass` is in the app's namespace.</span></span>

<span data-ttu-id="344f6-130">Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента:</span><span class="sxs-lookup"><span data-stu-id="344f6-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="344f6-131">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="344f6-131">Is prerendered into the page.</span></span>
* <span data-ttu-id="344f6-132">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="344f6-132">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="344f6-133">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="344f6-133">Render Mode</span></span> | <span data-ttu-id="344f6-134">Описание</span><span class="sxs-lookup"><span data-stu-id="344f6-134">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="344f6-135">Преобразует компонент в статический HTML и включает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="344f6-135">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="344f6-136">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="344f6-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="344f6-137">Отображает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="344f6-137">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="344f6-138">Выходные данные компонента не включаются.</span><span class="sxs-lookup"><span data-stu-id="344f6-138">Output from the component isn't included.</span></span> <span data-ttu-id="344f6-139">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="344f6-139">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="344f6-140">Преобразует компонент в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="344f6-140">Renders the component into static HTML.</span></span> |

<span data-ttu-id="344f6-141">Хотя страницы и представления могут использовать компоненты, наоборот это не так.</span><span class="sxs-lookup"><span data-stu-id="344f6-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="344f6-142">Компоненты не могут использовать функции представления и страницы, такие как частичные представления и разделы.</span><span class="sxs-lookup"><span data-stu-id="344f6-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="344f6-143">Чтобы использовать логику из частичного представления в компоненте, разнесите логику частичного представления в компонент.</span><span class="sxs-lookup"><span data-stu-id="344f6-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="344f6-144">Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="344f6-144">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="344f6-145">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="344f6-145">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
