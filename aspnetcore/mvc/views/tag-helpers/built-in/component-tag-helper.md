---
title: Вспомогательная функция тега компонента в ASP.NET Core
author: guardrex
ms.author: riande
description: 'Узнайте, как использовать вспомогательную функцию тега компонента ASP.NET Core для отрисовки :::no-loc(Razor)::: компонентов на страницах и в представлениях.'
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595458"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="f1d46-103">Вспомогательная функция тега компонента в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f1d46-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="f1d46-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f1d46-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f1d46-105">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="f1d46-105">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f1d46-106">Следуйте указаниям в разделе *конфигурации* :</span><span class="sxs-lookup"><span data-stu-id="f1d46-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [:::no-loc(Blazor WebAssembly):::](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [:::no-loc(Blazor Server):::](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f1d46-107">Следуйте указаниям в разделе *конфигурации* <xref:blazor/components/prerendering-and-integration?pivots=server> статьи.</span><span class="sxs-lookup"><span data-stu-id="f1d46-107">Follow the guidance in the *Configuration* section of the <xref:blazor/components/prerendering-and-integration?pivots=server> article.</span></span>

::: moniker-end

## <a name="component-tag-helper"></a><span data-ttu-id="f1d46-108">Вспомогательная функция тега компонента</span><span class="sxs-lookup"><span data-stu-id="f1d46-108">Component Tag Helper</span></span>

<span data-ttu-id="f1d46-109">Чтобы отобразить компонент из страницы или представления, используйте [вспомогательную функцию тега компонента](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` тег).</span><span class="sxs-lookup"><span data-stu-id="f1d46-109">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="f1d46-110">Интеграция :::no-loc(Razor)::: компонентов в :::no-loc(Razor)::: страницы и приложения MVC в *размещенном :::no-loc(Blazor WebAssembly)::: приложении* поддерживается в ASP.NET Core в .NET 5,0 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="f1d46-110">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a *hosted :::no-loc(Blazor WebAssembly)::: app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="f1d46-111">Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента:</span><span class="sxs-lookup"><span data-stu-id="f1d46-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="f1d46-112">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="f1d46-112">Is prerendered into the page.</span></span>
* <span data-ttu-id="f1d46-113">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения :::no-loc(Blazor)::: из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="f1d46-113">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f1d46-114">:::no-loc(Blazor WebAssembly)::: режимы отрисовки приложений показаны в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="f1d46-114">:::no-loc(Blazor WebAssembly)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="f1d46-115">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="f1d46-115">Render Mode</span></span> | <span data-ttu-id="f1d46-116">Описание</span><span class="sxs-lookup"><span data-stu-id="f1d46-116">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="f1d46-117">Подготавливает к просмотру маркер для :::no-loc(Blazor WebAssembly)::: приложения, который используется для включения интерактивного компонента при загрузке в браузере.</span><span class="sxs-lookup"><span data-stu-id="f1d46-117">Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="f1d46-118">Компонент не предварительно отображен.</span><span class="sxs-lookup"><span data-stu-id="f1d46-118">The component isn't prerendered.</span></span> <span data-ttu-id="f1d46-119">Этот параметр упрощает отображение различных :::no-loc(Blazor WebAssembly)::: компонентов на разных страницах.</span><span class="sxs-lookup"><span data-stu-id="f1d46-119">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="f1d46-120">Предварительно отображает компонент в виде статического кода HTML и включает маркер для :::no-loc(Blazor WebAssembly)::: приложения для последующего использования, чтобы сделать компонент интерактивным при загрузке в браузере.</span><span class="sxs-lookup"><span data-stu-id="f1d46-120">Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="f1d46-121">:::no-loc(Blazor Server)::: режимы отрисовки приложений показаны в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="f1d46-121">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="f1d46-122">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="f1d46-122">Render Mode</span></span> | <span data-ttu-id="f1d46-123">Описание</span><span class="sxs-lookup"><span data-stu-id="f1d46-123">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="f1d46-124">Преобразует компонент в статический HTML и включает метку приложения :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="f1d46-124">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="f1d46-125">При запуске пользовательского агента эта метка используется для начальной загрузки приложения :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="f1d46-125">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="f1d46-126">Отображает метку приложения :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="f1d46-126">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="f1d46-127">Выходные данные компонента не включаются.</span><span class="sxs-lookup"><span data-stu-id="f1d46-127">Output from the component isn't included.</span></span> <span data-ttu-id="f1d46-128">При запуске пользовательского агента эта метка используется для начальной загрузки приложения :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="f1d46-128">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="f1d46-129">Преобразует компонент в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="f1d46-129">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f1d46-130">:::no-loc(Blazor Server)::: режимы отрисовки приложений показаны в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="f1d46-130">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="f1d46-131">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="f1d46-131">Render Mode</span></span> | <span data-ttu-id="f1d46-132">Описание</span><span class="sxs-lookup"><span data-stu-id="f1d46-132">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="f1d46-133">Преобразует компонент в статический HTML и включает метку приложения :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="f1d46-133">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="f1d46-134">При запуске пользовательского агента эта метка используется для начальной загрузки приложения :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="f1d46-134">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="f1d46-135">Отображает метку приложения :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="f1d46-135">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="f1d46-136">Выходные данные компонента не включаются.</span><span class="sxs-lookup"><span data-stu-id="f1d46-136">Output from the component isn't included.</span></span> <span data-ttu-id="f1d46-137">При запуске пользовательского агента эта метка используется для начальной загрузки приложения :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="f1d46-137">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="f1d46-138">Преобразует компонент в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="f1d46-138">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="f1d46-139">К дополнительным характеристикам относятся:</span><span class="sxs-lookup"><span data-stu-id="f1d46-139">Additional characteristics include:</span></span>

* <span data-ttu-id="f1d46-140">Допускается использование нескольких вспомогательных функций тегов компонентов для визуализации нескольких :::no-loc(Razor)::: компонентов.</span><span class="sxs-lookup"><span data-stu-id="f1d46-140">Multiple Component Tag Helpers rendering multiple :::no-loc(Razor)::: components is allowed.</span></span>
* <span data-ttu-id="f1d46-141">Компоненты не могут быть динамически визуализированы после запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="f1d46-141">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="f1d46-142">Хотя страницы и представления могут использовать компоненты, наоборот это не так.</span><span class="sxs-lookup"><span data-stu-id="f1d46-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="f1d46-143">Компоненты не могут использовать функции представления и страницы, такие как частичные представления и разделы.</span><span class="sxs-lookup"><span data-stu-id="f1d46-143">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="f1d46-144">Чтобы использовать логику из частичного представления в компоненте, разнесите логику частичного представления в компонент.</span><span class="sxs-lookup"><span data-stu-id="f1d46-144">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="f1d46-145">Отрисовка компонентов сервера из статической HTML-страницы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="f1d46-145">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="f1d46-146">Следующая вспомогательная функция тега компонента визуализирует `Counter` компонент на странице или в представлении в :::no-loc(Blazor Server)::: приложении с помощью `ServerPrerendered` :</span><span class="sxs-lookup"><span data-stu-id="f1d46-146">The following Component Tag Helper renders the `Counter` component in a page or view in a :::no-loc(Blazor Server)::: app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="f1d46-147">В предыдущем примере предполагается, что `Counter` компонент находится в папке *pages* приложения.</span><span class="sxs-lookup"><span data-stu-id="f1d46-147">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="f1d46-148">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `@using :::no-loc(Blazor):::Sample.Pages` или `@using :::no-loc(Blazor):::Sample.Client.Pages` в размещенном :::no-loc(Blazor)::: решении).</span><span class="sxs-lookup"><span data-stu-id="f1d46-148">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Pages` or `@using :::no-loc(Blazor):::Sample.Client.Pages` in a hosted :::no-loc(Blazor)::: solution).</span></span>

<span data-ttu-id="f1d46-149">Вспомогательная функция тега компонента также может передавать параметры в компоненты.</span><span class="sxs-lookup"><span data-stu-id="f1d46-149">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="f1d46-150">Рассмотрим следующий `ColorfulCheckbox` компонент, устанавливающий цвет и размер метки флажка:</span><span class="sxs-lookup"><span data-stu-id="f1d46-150">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying :::no-loc(Blazor):::?
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

<span data-ttu-id="f1d46-151">`Size` `int` Параметры компонента () `Color` и `string` ( [component parameters](xref:blazor/components/index#component-parameters) ) могут быть заданы вспомогательной функцией тега компонента:</span><span class="sxs-lookup"><span data-stu-id="f1d46-151">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="f1d46-152">В предыдущем примере предполагается, что `ColorfulCheckbox` компонент находится в *общей* папке приложения.</span><span class="sxs-lookup"><span data-stu-id="f1d46-152">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="f1d46-153">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `@using :::no-loc(Blazor):::Sample.Shared`).</span><span class="sxs-lookup"><span data-stu-id="f1d46-153">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Shared`).</span></span>

<span data-ttu-id="f1d46-154">Следующий код HTML отображается на странице или в представлении:</span><span class="sxs-lookup"><span data-stu-id="f1d46-154">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying :::no-loc(Blazor):::?
</label>
```

<span data-ttu-id="f1d46-155">Для передачи строки в кавычках требуется [явное :::no-loc(Razor)::: выражение](xref:mvc/views/razor#explicit-razor-expressions), как показано `param-Color` в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="f1d46-155">Passing a quoted string requires an [explicit :::no-loc(Razor)::: expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="f1d46-156">:::no-loc(Razor):::Поведение синтаксического анализа для `string` значения типа не применяется к `param-*` атрибуту, так как атрибут является `object` типом.</span><span class="sxs-lookup"><span data-stu-id="f1d46-156">The :::no-loc(Razor)::: parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="f1d46-157">Поддерживаются все типы параметров, за исключением следующих:</span><span class="sxs-lookup"><span data-stu-id="f1d46-157">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="f1d46-158">Универсальные параметры.</span><span class="sxs-lookup"><span data-stu-id="f1d46-158">Generic parameters.</span></span>
* <span data-ttu-id="f1d46-159">Несериализуемые параметры.</span><span class="sxs-lookup"><span data-stu-id="f1d46-159">Non-serializable parameters.</span></span>
* <span data-ttu-id="f1d46-160">Наследование в параметрах коллекции.</span><span class="sxs-lookup"><span data-stu-id="f1d46-160">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="f1d46-161">Параметры, тип которых определен вне :::no-loc(Blazor WebAssembly)::: приложения или в неактивно загруженной сборке.</span><span class="sxs-lookup"><span data-stu-id="f1d46-161">Parameters whose type is defined outside of the :::no-loc(Blazor WebAssembly)::: app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="f1d46-162">Тип параметра должен быть сериализуемым JSON, что обычно означает, что тип должен иметь конструктор по умолчанию и устанавливаемые свойства.</span><span class="sxs-lookup"><span data-stu-id="f1d46-162">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="f1d46-163">Например, можно указать значение для `Size` и `Color` в предыдущем примере, так как типы `Size` и `Color` являются примитивными типами ( `int` и `string` ), которые поддерживаются сериализатором JSON.</span><span class="sxs-lookup"><span data-stu-id="f1d46-163">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="f1d46-164">В следующем примере объект класса передается в компонент:</span><span class="sxs-lookup"><span data-stu-id="f1d46-164">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="f1d46-165">*MyClass.CS* :</span><span class="sxs-lookup"><span data-stu-id="f1d46-165">*MyClass.cs* :</span></span>

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

<span data-ttu-id="f1d46-166">**Класс должен иметь открытый конструктор без параметров.**</span><span class="sxs-lookup"><span data-stu-id="f1d46-166">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="f1d46-167">*Shared/MyComponent. Razor* :</span><span class="sxs-lookup"><span data-stu-id="f1d46-167">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="f1d46-168">*Pages/MyPage. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f1d46-168">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="f1d46-169">В предыдущем примере предполагается, что `MyComponent` компонент находится в *общей* папке приложения.</span><span class="sxs-lookup"><span data-stu-id="f1d46-169">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="f1d46-170">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `@using :::no-loc(Blazor):::Sample` и `@using :::no-loc(Blazor):::Sample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="f1d46-170">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample` and `@using :::no-loc(Blazor):::Sample.Shared`).</span></span> <span data-ttu-id="f1d46-171">`MyClass` находится в пространстве имен приложения.</span><span class="sxs-lookup"><span data-stu-id="f1d46-171">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1d46-172">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f1d46-172">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
