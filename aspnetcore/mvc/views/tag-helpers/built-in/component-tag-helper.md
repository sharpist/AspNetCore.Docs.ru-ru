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
# <a name="component-tag-helper-in-aspnet-core"></a>Вспомогательная функция тега компонента в ASP.NET Core

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

Чтобы отобразить компонент из страницы или представления, используйте [вспомогательную функцию тега компонента](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).

## <a name="prerequisites"></a>Предварительные условия

Следуйте указаниям в разделе *Подготовка приложения к использованию компонентов в страницах и представлениях* <xref:blazor/integrate-components#prepare-the-app> статьи.

## <a name="component-tag-helper"></a>Вспомогательная функция тега компонента

Следующая вспомогательная функция тега Component визуализирует `Counter` компонент на странице или в представлении:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

В предыдущем примере предполагается, `Counter` что компонент находится в папке *pages* приложения.

Вспомогательная функция тега компонента также может передавать параметры в компоненты. Рассмотрим следующий `ColorfulCheckbox` компонент, устанавливающий цвет и размер метки флажка:

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

`Size` [Параметры компонента](xref:blazor/components#component-parameters) (`int`) `Color` и`string`() могут быть заданы вспомогательной функцией тега компонента:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

В предыдущем примере предполагается, `ColorfulCheckbox` что компонент находится в *общей* папке приложения.

Следующий код HTML отображается на странице или в представлении:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Для `param-Color` передачи строки в кавычках требуется [явное выражение Razor](xref:mvc/views/razor#explicit-razor-expressions), как показано в предыдущем примере. Поведение синтаксического анализа Razor для значения `string` типа не применяется к `param-*` атрибуту, так как атрибут является `object` типом.

Тип параметра должен быть сериализуемым JSON, что обычно означает, что тип должен иметь конструктор по умолчанию и устанавливаемые свойства. Например, можно указать значение для `Size` и `Color` в предыдущем примере, так как типы `Size` и `Color` являются примитивными типами (`int` и `string`), которые поддерживаются сериализатором JSON.

В следующем примере объект класса передается в компонент:

*MyClass.CS*:

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

*Shared/MyComponent. Razor*:

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

*Pages/MyPage. cshtml*:

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

В предыдущем примере предполагается, `MyComponent` что компонент находится в *общей* папке приложения. `MyClass`находится в пространстве имен приложения (`{APP ASSEMBLY}`).

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Настраивает, является ли компонент:

* Предварительно отображается на странице.
* Отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Блазор из агента пользователя.

| Режим рендеринга | Описание |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Преобразует компонент в статический HTML и включает маркер для Blazor серверного приложения. При запуске агента пользователя этот маркер используется для начальной загрузки Blazor приложения. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Отображает маркер для Blazor серверного приложения. Выходные данные компонента не включаются. При запуске агента пользователя этот маркер используется для начальной загрузки Blazor приложения. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Преобразует компонент в статический HTML. |

Хотя страницы и представления могут использовать компоненты, наоборот это не так. Компоненты не могут использовать функции представления и страницы, такие как частичные представления и разделы. Чтобы использовать логику из частичного представления в компоненте, разнесите логику частичного представления в компонент.

Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
