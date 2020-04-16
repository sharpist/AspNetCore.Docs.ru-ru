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
# <a name="component-tag-helper-in-aspnet-core"></a>Помощник тега компонента в ASP.NET ядре

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

Чтобы отобразить компонент со страницы или представления, используйте [Компонентный тег Helper.](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)

## <a name="prerequisites"></a>Предварительные требования

Следуйте инструкциям в *разделе Подготовка приложения к использованию компонентов на страницах и представлениях* в разделе статьи. <xref:blazor/integrate-components#prepare-the-app>

## <a name="component-tag-helper"></a>Помощник тега компонента

Следующий помощник тега компонента отображает `Counter` компонент на странице или представлении:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Предыдущий пример предполагает, `Counter` что компонент находится в папке *Страницы* приложения.

Помощник тега компонента также может передавать параметры компонентам. Рассмотрим `ColorfulCheckbox` следующий компонент, который устанавливает цвет и размер метки флажка:

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

`Size` Параметры `Color` компонента могут`string``int`быть [установлены Помощником](xref:blazor/components#component-parameters) компонентного тега:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

Предыдущий пример предполагает, `ColorfulCheckbox` что компонент находится в папке *«Общая* часть» приложения.

Следующий HTML отображается на странице или представлении:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Прохождение цитируемого строки требует [явного выражения Razor,](xref:mvc/views/razor#explicit-razor-expressions)как показано в `param-Color` предыдущем примере. Поведение разбора бритвы `string` для значения типа не `param-*` применяется к атрибуту, поскольку атрибут является типом. `object`

Тип параметра должен быть серийным JSON, что обычно означает, что тип должен иметь конструктор по умолчанию и свойства settable. Например, можно указать `Size` значение `Color` для и в предыдущем `Size` `Color` примере,`int` потому что типы и примитивные типы (и), `string`которые поддерживаются jSON serializer.

В следующем примере объект класса передается компоненту:

*MyClass.cs*:

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

**Класс должен иметь открытый конструктор без параметров.**

*Общие/MyComponent.razor*:

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

*Страницы/MyPage.cshtml*:

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

Предыдущий пример предполагает, `MyComponent` что компонент находится в папке *«Общая* часть» приложения. `MyClass`находится в пространстве имен приложения`{APP ASSEMBLY}`().

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>настраивает ли компонент:

* Предварительно отображается на странице.
* Отображается как статический HTML на странице или если он включает в себя необходимую информацию для загрузки приложения Blazor от агента пользователя.

| Режим рендеринга | Описание |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders компонент в статический HTML и Blazor включает маркер для приложения Server. Когда пользователь-агент запускается, этот маркер используется Blazor для загрузки приложения. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Отображает маркер для Blazor приложения Server. Выход из компонента не включен. Когда пользователь-агент запускается, этот маркер используется Blazor для загрузки приложения. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Рендеринг компонента в статический HTML. |

Хотя страницы и представления могут использовать компоненты, обратное не соответствует действительности. Компоненты не могут использовать функции, связанные с представлением и страницей, такие как частичные представления и разделы. Чтобы использовать логику из частичного представления в компоненте, увечись логику частичного представления в компонент.

Рендеринг компонентов сервера со статической html-страницы не поддерживается.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
