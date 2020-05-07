---
title: Шаблонные компоненты Blazor в ASP.NET Core
author: guardrex
description: Сведения о шаблонных компонентах, которые могут принимать один или несколько шаблонов пользовательского интерфейса в качестве параметров, используемых затем как часть логики отрисовки компонента.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: de603d3520c124b278312e5167a2f8bad14cf6e9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771071"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="51b44-103">Шаблонные компоненты Blazor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51b44-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="51b44-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="51b44-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="51b44-105">Шаблонные компоненты — это компоненты, которые могут принимать один или несколько шаблонов пользовательского интерфейса в качестве параметров, используемых затем как часть логики отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="51b44-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="51b44-106">Шаблонные компоненты позволяют разрабатывать более общие компоненты, которые удобнее использовать повторно, чем обычные.</span><span class="sxs-lookup"><span data-stu-id="51b44-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="51b44-107">Вот несколько примеров:</span><span class="sxs-lookup"><span data-stu-id="51b44-107">A couple of examples include:</span></span>

* <span data-ttu-id="51b44-108">компонент таблицы, позволяющий пользователю задавать шаблоны для заголовка, строк и нижнего колонтитула таблицы;</span><span class="sxs-lookup"><span data-stu-id="51b44-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="51b44-109">компонент списка, позволяющий пользователю задавать шаблон для отрисовываемых элементов списка.</span><span class="sxs-lookup"><span data-stu-id="51b44-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="51b44-110">Параметры шаблона</span><span class="sxs-lookup"><span data-stu-id="51b44-110">Template parameters</span></span>

<span data-ttu-id="51b44-111">Шаблонный компонент определяется путем указания одного или нескольких параметров компонента типа `RenderFragment` или `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="51b44-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="51b44-112">Фрагмент отрисовки представляет часть пользовательского интерфейса, подлежащую отрисовке.</span><span class="sxs-lookup"><span data-stu-id="51b44-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="51b44-113">`RenderFragment<T>` принимает параметр типа, который можно задать при вызове фрагмента отрисовки.</span><span class="sxs-lookup"><span data-stu-id="51b44-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="51b44-114">Компонент `TableTemplate`:</span><span class="sxs-lookup"><span data-stu-id="51b44-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="51b44-115">При использовании шаблонного компонента параметры шаблона можно задать с помощью дочерних элементов, имена которых совпадают с именами параметров (`TableHeader` и `RowTemplate` в следующем примере):</span><span class="sxs-lookup"><span data-stu-id="51b44-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

> [!NOTE]
> <span data-ttu-id="51b44-116">Ограничения универсального типа будут поддерживаться в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="51b44-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="51b44-117">Дополнительные сведения см. в разделе [Разрешение ограничений универсального типа (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="51b44-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="51b44-118">Параметры контекста шаблона</span><span class="sxs-lookup"><span data-stu-id="51b44-118">Template context parameters</span></span>

<span data-ttu-id="51b44-119">Аргументы компонента типа `RenderFragment<T>`, передаваемые как элементы, имеют неявный параметр `context` (например, `@context.PetId` в предыдущем примере кода), но его имя можно изменить с помощью атрибута `Context` дочернего элемента.</span><span class="sxs-lookup"><span data-stu-id="51b44-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="51b44-120">В следующем примере атрибут `RowTemplate` элемента `Context` задает имя параметра `pet`:</span><span class="sxs-lookup"><span data-stu-id="51b44-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="51b44-121">Кроме того, атрибут `Context` можно задать для элемента компонента.</span><span class="sxs-lookup"><span data-stu-id="51b44-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="51b44-122">Заданный атрибут `Context` применяется ко всем указанным параметрам шаблона.</span><span class="sxs-lookup"><span data-stu-id="51b44-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="51b44-123">Это может быть полезно, если необходимо указать имя параметра содержимого для неявного дочернего содержимого (без содержащего дочернего элемента).</span><span class="sxs-lookup"><span data-stu-id="51b44-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="51b44-124">В следующем примере элемент `Context` имеет атрибут `TableTemplate`, который применяется ко всем параметрам шаблона:</span><span class="sxs-lookup"><span data-stu-id="51b44-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="51b44-125">Компоненты универсального типа</span><span class="sxs-lookup"><span data-stu-id="51b44-125">Generic-typed components</span></span>

<span data-ttu-id="51b44-126">Шаблонные компоненты часто имеют универсальный тип.</span><span class="sxs-lookup"><span data-stu-id="51b44-126">Templated components are often generically typed.</span></span> <span data-ttu-id="51b44-127">Например, универсальный компонент `ListViewTemplate` можно использовать для отрисовки значений `IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="51b44-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="51b44-128">Чтобы определить универсальный компонент, используйте директиву [`@typeparam`](xref:mvc/views/razor#typeparam) и укажите параметры типа:</span><span class="sxs-lookup"><span data-stu-id="51b44-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="51b44-129">При использовании компонентов универсального типа параметр типа выводится, если это возможно:</span><span class="sxs-lookup"><span data-stu-id="51b44-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="51b44-130">В противном случае параметр типа необходимо указать явно с помощью атрибута, совпадающего с именем параметра типа.</span><span class="sxs-lookup"><span data-stu-id="51b44-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="51b44-131">В следующем примере `TItem="Pet"` задает тип:</span><span class="sxs-lookup"><span data-stu-id="51b44-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
