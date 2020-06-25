---
title: Каскадные значения и параметры ASP.NET Core Blazor
author: guardrex
description: Сведения о передаче данных из компонента предка в дочерние компоненты.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 70f379b3b0e48dbb340f319f3346bbbf44588740
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103476"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="e0fee-103">Каскадные значения и параметры ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e0fee-103">ASP.NET Core Blazor cascading values and parameters</span></span>

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="e0fee-104">Каскадные значения и параметры</span><span class="sxs-lookup"><span data-stu-id="e0fee-104">Cascading values and parameters</span></span>

<span data-ttu-id="e0fee-105">В некоторых сценариях неудобно передавать данные из компонента-предка в компонент-потомок, используя [параметры компонента](xref:blazor/components/index#component-parameters), особенно если имеется несколько уровней компонентов.</span><span class="sxs-lookup"><span data-stu-id="e0fee-105">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](xref:blazor/components/index#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="e0fee-106">Каскадные значения и параметры позволяют решить эту проблему, предоставляя компоненту-предку удобный способ передать значение всем его компонентам-потомкам.</span><span class="sxs-lookup"><span data-stu-id="e0fee-106">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="e0fee-107">Каскадные значения и параметры также предоставляют подход для координации компонентов.</span><span class="sxs-lookup"><span data-stu-id="e0fee-107">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="e0fee-108">Пример темы</span><span class="sxs-lookup"><span data-stu-id="e0fee-108">Theme example</span></span>

<span data-ttu-id="e0fee-109">В следующем примере из примера приложения класс `ThemeInfo` указывает сведения о теме для передачи вниз по иерархии компонентов, чтобы все кнопки в пределах заданной части приложения использовали одинаковый стиль.</span><span class="sxs-lookup"><span data-stu-id="e0fee-109">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="e0fee-110">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="e0fee-110">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="e0fee-111">Компонент-предок может предоставлять каскадное значение с помощью компонента каскадного значения.</span><span class="sxs-lookup"><span data-stu-id="e0fee-111">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="e0fee-112">Компонент <xref:Microsoft.AspNetCore.Components.CascadingValue%601> упаковывает поддерево иерархии компонентов и предоставляет одно значение всем компонентам в этом поддереве.</span><span class="sxs-lookup"><span data-stu-id="e0fee-112">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="e0fee-113">Например, пример приложения указывает сведения о теме (`ThemeInfo`) в одном из макетов приложения в виде каскадного параметра для всех компонентов, составляющих основной текст макета свойства `@Body`.</span><span class="sxs-lookup"><span data-stu-id="e0fee-113">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="e0fee-114">`ButtonClass` присваивается значение `btn-success` в компоненте макета.</span><span class="sxs-lookup"><span data-stu-id="e0fee-114">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="e0fee-115">Любой компонент-потомок может использовать это свойство через каскадный объект `ThemeInfo`.</span><span class="sxs-lookup"><span data-stu-id="e0fee-115">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="e0fee-116">Компонент `CascadingValuesParametersLayout`:</span><span class="sxs-lookup"><span data-stu-id="e0fee-116">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="e0fee-117">Чтобы использовать каскадные значения, компоненты объявляют каскадные параметры с помощью атрибута [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="e0fee-117">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="e0fee-118">Каскадные значения привязаны к каскадным параметрам по типу.</span><span class="sxs-lookup"><span data-stu-id="e0fee-118">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="e0fee-119">В примере приложения компонент `CascadingValuesParametersTheme` привязывает каскадное значение `ThemeInfo` к каскадному параметру.</span><span class="sxs-lookup"><span data-stu-id="e0fee-119">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="e0fee-120">Параметр используется для задания класса CSS для одной из кнопок, отображаемых компонентом.</span><span class="sxs-lookup"><span data-stu-id="e0fee-120">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="e0fee-121">Компонент `CascadingValuesParametersTheme`:</span><span class="sxs-lookup"><span data-stu-id="e0fee-121">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="e0fee-122">Чтобы каскадировать несколько значений одного типа в одном поддереве, укажите уникальную строку <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> для каждого компонента <xref:Microsoft.AspNetCore.Components.CascadingValue%601> и его соответствующего атрибута [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="e0fee-122">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="e0fee-123">В следующем примере два компонента <xref:Microsoft.AspNetCore.Components.CascadingValue%601> каскадируют разные экземпляры `MyCascadingType` по имени:</span><span class="sxs-lookup"><span data-stu-id="e0fee-123">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="e0fee-124">В компоненте-потомке каскадные параметры получают значения из соответствующих каскадных значений в компоненте-предке по имени:</span><span class="sxs-lookup"><span data-stu-id="e0fee-124">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="e0fee-125">Пример TabSet</span><span class="sxs-lookup"><span data-stu-id="e0fee-125">TabSet example</span></span>

<span data-ttu-id="e0fee-126">Каскадные параметры также позволяют компонентам взаимодействовать в рамках иерархии компонентов.</span><span class="sxs-lookup"><span data-stu-id="e0fee-126">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="e0fee-127">Например, рассмотрим следующий пример *TabSet* в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="e0fee-127">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="e0fee-128">Пример приложения содержит интерфейс `ITab`, реализуемый вкладками:</span><span class="sxs-lookup"><span data-stu-id="e0fee-128">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="e0fee-129">Компонент `CascadingValuesParametersTabSet` использует компонент `TabSet`, содержащий несколько компонентов `Tab`:</span><span class="sxs-lookup"><span data-stu-id="e0fee-129">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="e0fee-130">Дочерние компоненты `Tab` не передаются в `TabSet` в качестве параметров явным образом.</span><span class="sxs-lookup"><span data-stu-id="e0fee-130">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="e0fee-131">Вместо этого дочерние компоненты `Tab` являются частью дочернего содержимого `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="e0fee-131">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="e0fee-132">Однако `TabSet` по-прежнему необходимо знать о каждом компоненте `Tab`, чтобы визуализировать заголовки и активную вкладку. Чтобы обеспечить такую координацию без дополнительного кода, компонент `TabSet` *может предоставить себя в качестве каскадного значения*, которое затем используется компонентами-потомками `Tab`.</span><span class="sxs-lookup"><span data-stu-id="e0fee-132">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="e0fee-133">Компонент `TabSet`:</span><span class="sxs-lookup"><span data-stu-id="e0fee-133">`TabSet` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="e0fee-134">Компоненты-потомки `Tab` захватывают содержащий `TabSet` в качестве каскадного параметра, поэтому компоненты `Tab` добавляются в `TabSet` и координируют, какая вкладка активна.</span><span class="sxs-lookup"><span data-stu-id="e0fee-134">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="e0fee-135">Компонент `Tab`:</span><span class="sxs-lookup"><span data-stu-id="e0fee-135">`Tab` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]