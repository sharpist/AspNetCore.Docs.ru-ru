---
title: Вспомогательная функция тега компонента в ASP.NET Core
author: guardrex
ms.author: riande
description: Узнайте, как использовать вспомогательную функцию тега компонента ASP.NET Core для отрисовки Razor компонентов на страницах и в представлениях.
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 8e780de2367f66ad1f5197077d5243e0b85a41dd
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431047"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Вспомогательная функция тега компонента в ASP.NET Core

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

## <a name="prerequisites"></a>Предварительные требования

Следуйте указаниям в разделе *конфигурации* :

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

## <a name="component-tag-helper"></a>Вспомогательная функция тега компонента

Чтобы отобразить компонент из страницы или представления, используйте [вспомогательную функцию тега компонента](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` тег).

> [!NOTE]
> Интеграция Razor компонентов в Razor страницы и приложения MVC в *размещенном Blazor WebAssembly приложении* поддерживается в ASP.NET Core в .NET 5,0 или более поздней версии.

Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента:

* компонент предварительно преобразуется в страницу;
* компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly режимы отрисовки приложений показаны в следующей таблице.

| Режим обработки | Описание |
| ----------- | ----------- |
| `WebAssembly` | Подготавливает к просмотру маркер для Blazor WebAssembly приложения, который используется для включения интерактивного компонента при загрузке в браузере. Компонент не предварительно отображен. Этот параметр упрощает отображение различных Blazor WebAssembly компонентов на разных страницах. |
| `WebAssemblyPrerendered` | Предварительно отображает компонент в виде статического кода HTML и включает маркер для Blazor WebAssembly приложения для последующего использования, чтобы сделать компонент интерактивным при загрузке в браузере. |

Blazor Server режимы отрисовки приложений показаны в следующей таблице.

| Режим обработки | Описание |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Преобразует компонент в статический HTML и включает метку приложения Blazor Server. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Отображает метку приложения Blazor Server. Выходные данные компонента не включаются. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Преобразует компонент в статический HTML. |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Blazor Server режимы отрисовки приложений показаны в следующей таблице.

| Режим обработки | Описание |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Преобразует компонент в статический HTML и включает метку приложения Blazor Server. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Отображает метку приложения Blazor Server. Выходные данные компонента не включаются. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Преобразует компонент в статический HTML. |

::: moniker-end

К дополнительным характеристикам относятся:

* Допускается использование нескольких вспомогательных функций тегов компонентов для визуализации нескольких Razor компонентов.
* Компоненты не могут быть динамически визуализированы после запуска приложения.
* Хотя страницы и представления могут использовать компоненты, наоборот это не так. Компоненты не могут использовать функции представления и страницы, такие как частичные представления и разделы. Чтобы использовать логику из частичного представления в компоненте, разнесите логику частичного представления в компонент.
* Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.

Следующая вспомогательная функция тега компонента визуализирует `Counter` компонент на странице или в представлении в Blazor Server приложении с помощью `ServerPrerendered` :

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

В предыдущем примере предполагается, что `Counter` компонент находится в папке *pages* приложения. Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `@using BlazorSample.Pages` или `@using BlazorSample.Client.Pages` в размещенном Blazor решении).

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

`Size` `int` Параметры компонента () `Color` и `string` ( [component parameters](xref:blazor/components/index#component-parameters) ) могут быть заданы вспомогательной функцией тега компонента:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

В предыдущем примере предполагается, что `ColorfulCheckbox` компонент находится в *общей* папке приложения. Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `@using BlazorSample.Shared`).

Следующий код HTML отображается на странице или в представлении:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Для передачи строки в кавычках требуется [явное Razor выражение](xref:mvc/views/razor#explicit-razor-expressions), как показано `param-Color` в предыдущем примере. RazorПоведение синтаксического анализа для `string` значения типа не применяется к `param-*` атрибуту, так как атрибут является `object` типом.

Поддерживаются все типы параметров, за исключением следующих:

* Универсальные параметры.
* Несериализуемые параметры.
* Наследование в параметрах коллекции.
* Параметры, тип которых определен вне Blazor WebAssembly приложения или в неактивно загруженной сборке.

Тип параметра должен быть сериализуемым JSON, что обычно означает, что тип должен иметь конструктор по умолчанию и устанавливаемые свойства. Например, можно указать значение для `Size` и `Color` в предыдущем примере, так как типы `Size` и `Color` являются примитивными типами ( `int` и `string` ), которые поддерживаются сериализатором JSON.

В следующем примере объект класса передается в компонент:

*MyClass.CS* :

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

*Shared/MyComponent. Razor* :

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

*Pages/MyPage. cshtml* :

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

В предыдущем примере предполагается, что `MyComponent` компонент находится в *общей* папке приложения. Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `@using BlazorSample` и `@using BlazorSample.Shared` ). `MyClass` находится в пространстве имен приложения.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
