---
title: Помощник тега компонента в ASP.NET ядре
author: guardrex
ms.author: riande
description: Узнайте, как использовать ASP.NET core Component Tag Helper для визуализации компонентов Razor на страницах и представлениях.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: aaa4b92a8912b4f52d861ed07432aa7cf3ca5240
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440965"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="04d3f-103">Помощник тега компонента в ASP.NET ядре</span><span class="sxs-lookup"><span data-stu-id="04d3f-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="04d3f-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="04d3f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="04d3f-105">Чтобы отобразить компонент со страницы или представления, используйте [Компонентный тег Helper.](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)</span><span class="sxs-lookup"><span data-stu-id="04d3f-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="04d3f-106">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="04d3f-106">Prerequisites</span></span>

<span data-ttu-id="04d3f-107">Следуйте инструкциям в *разделе Подготовка приложения к использованию компонентов на страницах и представлениях* в разделе статьи. <xref:blazor/integrate-components#prepare-the-app></span><span class="sxs-lookup"><span data-stu-id="04d3f-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="04d3f-108">Помощник тега компонента</span><span class="sxs-lookup"><span data-stu-id="04d3f-108">Component Tag Helper</span></span>

<span data-ttu-id="04d3f-109">Следующий помощник тега компонента отображает `Counter` компонент на странице или представлении:</span><span class="sxs-lookup"><span data-stu-id="04d3f-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="04d3f-110">Предыдущий пример предполагает, `Counter` что компонент находится в папке *Страницы* приложения.</span><span class="sxs-lookup"><span data-stu-id="04d3f-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="04d3f-111">Помощник тега компонента также может передавать параметры компонентам.</span><span class="sxs-lookup"><span data-stu-id="04d3f-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="04d3f-112">Рассмотрим `ColorfulCheckbox` следующий компонент, который устанавливает цвет и размер метки флажка:</span><span class="sxs-lookup"><span data-stu-id="04d3f-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="04d3f-113">`Size` Параметры `Color` компонента могут`string``int`быть [установлены Помощником](xref:blazor/components#component-parameters) компонентного тега:</span><span class="sxs-lookup"><span data-stu-id="04d3f-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="04d3f-114">Предыдущий пример предполагает, `ColorfulCheckbox` что компонент находится в папке *«Общая* часть» приложения.</span><span class="sxs-lookup"><span data-stu-id="04d3f-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="04d3f-115">Следующий HTML отображается на странице или представлении:</span><span class="sxs-lookup"><span data-stu-id="04d3f-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="04d3f-116">Прохождение цитируемого строки требует [явного выражения Razor,](xref:mvc/views/razor#explicit-razor-expressions)как показано в `param-Color` предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="04d3f-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="04d3f-117">Поведение разбора бритвы `string` для значения типа не `param-*` применяется к атрибуту, поскольку атрибут является типом. `object`</span><span class="sxs-lookup"><span data-stu-id="04d3f-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="04d3f-118">Тип параметра должен быть серийным JSON, что обычно означает, что тип должен иметь конструктор по умолчанию и свойства settable.</span><span class="sxs-lookup"><span data-stu-id="04d3f-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="04d3f-119">Например, можно указать `Size` значение `Color` для и в предыдущем `Size` `Color` примере,`int` потому что типы и примитивные типы (и), `string`которые поддерживаются jSON serializer.</span><span class="sxs-lookup"><span data-stu-id="04d3f-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="04d3f-120">В следующем примере объект класса передается компоненту:</span><span class="sxs-lookup"><span data-stu-id="04d3f-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="04d3f-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="04d3f-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="04d3f-122">**Класс должен иметь открытый конструктор без параметров.**</span><span class="sxs-lookup"><span data-stu-id="04d3f-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="04d3f-123">*Общие/MyComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="04d3f-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="04d3f-124">*Страницы/MyPage.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="04d3f-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="04d3f-125">Предыдущий пример предполагает, `MyComponent` что компонент находится в папке *«Общая* часть» приложения.</span><span class="sxs-lookup"><span data-stu-id="04d3f-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="04d3f-126">`MyClass`находится в пространстве имен приложения`{APP ASSEMBLY}`().</span><span class="sxs-lookup"><span data-stu-id="04d3f-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="04d3f-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>настраивает ли компонент:</span><span class="sxs-lookup"><span data-stu-id="04d3f-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="04d3f-128">Предварительно отображается на странице.</span><span class="sxs-lookup"><span data-stu-id="04d3f-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="04d3f-129">Отображается как статический HTML на странице или если он включает в себя необходимую информацию для загрузки приложения Blazor от агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="04d3f-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="04d3f-130">Режим рендеринга</span><span class="sxs-lookup"><span data-stu-id="04d3f-130">Render Mode</span></span> | <span data-ttu-id="04d3f-131">Описание</span><span class="sxs-lookup"><span data-stu-id="04d3f-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="04d3f-132">Renders компонент в статический HTML и Blazor включает маркер для приложения Server.</span><span class="sxs-lookup"><span data-stu-id="04d3f-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="04d3f-133">Когда пользователь-агент запускается, этот маркер используется Blazor для загрузки приложения.</span><span class="sxs-lookup"><span data-stu-id="04d3f-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="04d3f-134">Отображает маркер для Blazor приложения Server.</span><span class="sxs-lookup"><span data-stu-id="04d3f-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="04d3f-135">Выход из компонента не включен.</span><span class="sxs-lookup"><span data-stu-id="04d3f-135">Output from the component isn't included.</span></span> <span data-ttu-id="04d3f-136">Когда пользователь-агент запускается, этот маркер используется Blazor для загрузки приложения.</span><span class="sxs-lookup"><span data-stu-id="04d3f-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="04d3f-137">Рендеринг компонента в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="04d3f-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="04d3f-138">Хотя страницы и представления могут использовать компоненты, обратное не соответствует действительности.</span><span class="sxs-lookup"><span data-stu-id="04d3f-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="04d3f-139">Компоненты не могут использовать функции, связанные с представлением и страницей, такие как частичные представления и разделы.</span><span class="sxs-lookup"><span data-stu-id="04d3f-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="04d3f-140">Чтобы использовать логику из частичного представления в компоненте, увечись логику частичного представления в компонент.</span><span class="sxs-lookup"><span data-stu-id="04d3f-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="04d3f-141">Рендеринг компонентов сервера со статической html-страницы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="04d3f-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="04d3f-142">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="04d3f-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
