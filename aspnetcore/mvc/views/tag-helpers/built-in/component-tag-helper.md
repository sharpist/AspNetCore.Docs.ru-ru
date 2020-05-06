---
title: Вспомогательная функция тега компонента в ASP.NET Core
author: guardrex
ms.author: riande
description: Узнайте, как использовать вспомогательную функцию тега компонента ASP.NET Core для Razor отрисовки компонентов на страницах и в представлениях.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4e003e5ed5e7863d8a218c0f02bb37e214e31910
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773933"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="00b22-103">Вспомогательная функция тега компонента в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00b22-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="00b22-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="00b22-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="00b22-105">Чтобы отобразить компонент из страницы или представления, используйте [вспомогательную функцию тега компонента](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="00b22-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="00b22-106">Предварительные условия</span><span class="sxs-lookup"><span data-stu-id="00b22-106">Prerequisites</span></span>

<span data-ttu-id="00b22-107">Следуйте указаниям в разделе *Подготовка приложения к использованию компонентов в страницах и представлениях* <xref:blazor/integrate-components#prepare-the-app> статьи.</span><span class="sxs-lookup"><span data-stu-id="00b22-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="00b22-108">Вспомогательная функция тега компонента</span><span class="sxs-lookup"><span data-stu-id="00b22-108">Component Tag Helper</span></span>

<span data-ttu-id="00b22-109">Следующая вспомогательная функция тега Component визуализирует `Counter` компонент на странице или в представлении:</span><span class="sxs-lookup"><span data-stu-id="00b22-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="00b22-110">В предыдущем примере предполагается, `Counter` что компонент находится в папке *pages* приложения.</span><span class="sxs-lookup"><span data-stu-id="00b22-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="00b22-111">Вспомогательная функция тега компонента также может передавать параметры в компоненты.</span><span class="sxs-lookup"><span data-stu-id="00b22-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="00b22-112">Рассмотрим следующий `ColorfulCheckbox` компонент, устанавливающий цвет и размер метки флажка:</span><span class="sxs-lookup"><span data-stu-id="00b22-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="00b22-113">`Size` [Параметры компонента](xref:blazor/components#component-parameters) (`int`) `Color` и`string`() могут быть заданы вспомогательной функцией тега компонента:</span><span class="sxs-lookup"><span data-stu-id="00b22-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="00b22-114">В предыдущем примере предполагается, `ColorfulCheckbox` что компонент находится в *общей* папке приложения.</span><span class="sxs-lookup"><span data-stu-id="00b22-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="00b22-115">Следующий код HTML отображается на странице или в представлении:</span><span class="sxs-lookup"><span data-stu-id="00b22-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="00b22-116">Для `param-Color` передачи строки в кавычках требуется [явное выражение Razor](xref:mvc/views/razor#explicit-razor-expressions), как показано в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="00b22-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="00b22-117">Поведение синтаксического анализа Razor для значения `string` типа не применяется к `param-*` атрибуту, так как атрибут является `object` типом.</span><span class="sxs-lookup"><span data-stu-id="00b22-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="00b22-118">Тип параметра должен быть сериализуемым JSON, что обычно означает, что тип должен иметь конструктор по умолчанию и устанавливаемые свойства.</span><span class="sxs-lookup"><span data-stu-id="00b22-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="00b22-119">Например, можно указать значение для `Size` и `Color` в предыдущем примере, так как типы `Size` и `Color` являются примитивными типами (`int` и `string`), которые поддерживаются сериализатором JSON.</span><span class="sxs-lookup"><span data-stu-id="00b22-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="00b22-120">В следующем примере объект класса передается в компонент:</span><span class="sxs-lookup"><span data-stu-id="00b22-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="00b22-121">*MyClass.CS*:</span><span class="sxs-lookup"><span data-stu-id="00b22-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="00b22-122">**Класс должен иметь открытый конструктор без параметров.**</span><span class="sxs-lookup"><span data-stu-id="00b22-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="00b22-123">*Shared/MyComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="00b22-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="00b22-124">*Pages/MyPage. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="00b22-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="00b22-125">В предыдущем примере предполагается, `MyComponent` что компонент находится в *общей* папке приложения.</span><span class="sxs-lookup"><span data-stu-id="00b22-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="00b22-126">`MyClass`находится в пространстве имен приложения (`{APP ASSEMBLY}`).</span><span class="sxs-lookup"><span data-stu-id="00b22-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="00b22-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Настраивает, является ли компонент:</span><span class="sxs-lookup"><span data-stu-id="00b22-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="00b22-128">Предварительно отображается на странице.</span><span class="sxs-lookup"><span data-stu-id="00b22-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="00b22-129">Отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Блазор из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="00b22-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="00b22-130">Режим рендеринга</span><span class="sxs-lookup"><span data-stu-id="00b22-130">Render Mode</span></span> | <span data-ttu-id="00b22-131">Описание</span><span class="sxs-lookup"><span data-stu-id="00b22-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="00b22-132">Преобразует компонент в статический HTML и включает маркер для Blazor серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="00b22-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="00b22-133">При запуске агента пользователя этот маркер используется для начальной загрузки Blazor приложения.</span><span class="sxs-lookup"><span data-stu-id="00b22-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="00b22-134">Отображает маркер для Blazor серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="00b22-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="00b22-135">Выходные данные компонента не включаются.</span><span class="sxs-lookup"><span data-stu-id="00b22-135">Output from the component isn't included.</span></span> <span data-ttu-id="00b22-136">При запуске агента пользователя этот маркер используется для начальной загрузки Blazor приложения.</span><span class="sxs-lookup"><span data-stu-id="00b22-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="00b22-137">Преобразует компонент в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="00b22-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="00b22-138">Хотя страницы и представления могут использовать компоненты, наоборот это не так.</span><span class="sxs-lookup"><span data-stu-id="00b22-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="00b22-139">Компоненты не могут использовать функции представления и страницы, такие как частичные представления и разделы.</span><span class="sxs-lookup"><span data-stu-id="00b22-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="00b22-140">Чтобы использовать логику из частичного представления в компоненте, разнесите логику частичного представления в компонент.</span><span class="sxs-lookup"><span data-stu-id="00b22-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="00b22-141">Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="00b22-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00b22-142">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="00b22-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
