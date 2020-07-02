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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: c72bf5584c7e0afdeadbb4103b5e58d91ae8c1e9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399222"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a>Каскадные значения и параметры ASP.NET Core Blazor

## <a name="cascading-values-and-parameters"></a>Каскадные значения и параметры

В некоторых сценариях неудобно передавать данные из компонента-предка в компонент-потомок, используя [параметры компонента](xref:blazor/components/index#component-parameters), особенно если имеется несколько уровней компонентов. Каскадные значения и параметры позволяют решить эту проблему, предоставляя компоненту-предку удобный способ передать значение всем его компонентам-потомкам. Каскадные значения и параметры также предоставляют подход для координации компонентов.

### <a name="theme-example"></a>Пример темы

В следующем примере из примера приложения класс `ThemeInfo` указывает сведения о теме для передачи вниз по иерархии компонентов, чтобы все кнопки в пределах заданной части приложения использовали одинаковый стиль.

`UIThemeClasses/ThemeInfo.cs`.

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Компонент-предок может предоставлять каскадное значение с помощью компонента каскадного значения. Компонент <xref:Microsoft.AspNetCore.Components.CascadingValue%601> упаковывает поддерево иерархии компонентов и предоставляет одно значение всем компонентам в этом поддереве.

Например, пример приложения указывает сведения о теме (`ThemeInfo`) в одном из макетов приложения в виде каскадного параметра для всех компонентов, составляющих основной текст макета свойства `@Body`. `ButtonClass` присваивается значение `btn-success` в компоненте макета. Любой компонент-потомок может использовать это свойство через каскадный объект `ThemeInfo`.

Компонент `CascadingValuesParametersLayout`:

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

Чтобы использовать каскадные значения, компоненты объявляют каскадные параметры с помощью атрибута [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute). Каскадные значения привязаны к каскадным параметрам по типу.

В примере приложения компонент `CascadingValuesParametersTheme` привязывает каскадное значение `ThemeInfo` к каскадному параметру. Параметр используется для задания класса CSS для одной из кнопок, отображаемых компонентом.

Компонент `CascadingValuesParametersTheme`:

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

Чтобы каскадировать несколько значений одного типа в одном поддереве, укажите уникальную строку <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> для каждого компонента <xref:Microsoft.AspNetCore.Components.CascadingValue%601> и его соответствующего атрибута [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute). В следующем примере два компонента <xref:Microsoft.AspNetCore.Components.CascadingValue%601> каскадируют разные экземпляры `MyCascadingType` по имени:

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

В компоненте-потомке каскадные параметры получают значения из соответствующих каскадных значений в компоненте-предке по имени:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Пример TabSet

Каскадные параметры также позволяют компонентам взаимодействовать в рамках иерархии компонентов. Например, рассмотрим следующий пример `TabSet` в примере приложения.

Пример приложения содержит интерфейс `ITab`, реализуемый вкладками:

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Компонент `CascadingValuesParametersTabSet` использует компонент `TabSet`, содержащий несколько компонентов `Tab`:

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

Дочерние компоненты `Tab` не передаются в `TabSet` в качестве параметров явным образом. Вместо этого дочерние компоненты `Tab` являются частью дочернего содержимого `TabSet`. Однако `TabSet` по-прежнему необходимо знать о каждом компоненте `Tab`, чтобы визуализировать заголовки и активную вкладку. Чтобы обеспечить такую координацию без дополнительного кода, компонент `TabSet` *может предоставить себя в качестве каскадного значения*, которое затем используется компонентами-потомками `Tab`.

Компонент `TabSet`:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Компоненты-потомки `Tab` захватывают содержащий `TabSet` в качестве каскадного параметра, поэтому компоненты `Tab` добавляются в `TabSet` и координируют, какая вкладка активна.

Компонент `Tab`:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]