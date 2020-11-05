---
title: 'Шаблонные компоненты :::no-loc(Blazor)::: в ASP.NET Core'
author: guardrex
description: Сведения о шаблонных компонентах, которые могут принимать один или несколько шаблонов пользовательского интерфейса в качестве параметров, используемых затем как часть логики отрисовки компонента.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
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
uid: blazor/components/templated-components
ms.openlocfilehash: ee38040a1edc8db43b8d5bf0202fd519b153ea04
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056248"
---
# <a name="aspnet-core-no-locblazor-templated-components"></a><span data-ttu-id="23053-103">Шаблонные компоненты :::no-loc(Blazor)::: в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23053-103">ASP.NET Core :::no-loc(Blazor)::: templated components</span></span>

<span data-ttu-id="23053-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="23053-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="23053-105">Шаблонные компоненты — это компоненты, которые могут принимать один или несколько шаблонов пользовательского интерфейса в качестве параметров, используемых затем как часть логики отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="23053-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="23053-106">Шаблонные компоненты позволяют разрабатывать более общие компоненты, которые удобнее использовать повторно, чем обычные.</span><span class="sxs-lookup"><span data-stu-id="23053-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="23053-107">Вот несколько примеров:</span><span class="sxs-lookup"><span data-stu-id="23053-107">A couple of examples include:</span></span>

* <span data-ttu-id="23053-108">компонент таблицы, позволяющий пользователю задавать шаблоны для заголовка, строк и нижнего колонтитула таблицы;</span><span class="sxs-lookup"><span data-stu-id="23053-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="23053-109">компонент списка, позволяющий пользователю задавать шаблон для отрисовываемых элементов списка.</span><span class="sxs-lookup"><span data-stu-id="23053-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="23053-110">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="23053-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="template-parameters"></a><span data-ttu-id="23053-111">Параметры шаблона</span><span class="sxs-lookup"><span data-stu-id="23053-111">Template parameters</span></span>

<span data-ttu-id="23053-112">Шаблонный компонент определяется путем указания одного или нескольких параметров компонента типа <xref:Microsoft.AspNetCore.Components.RenderFragment> или <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span><span class="sxs-lookup"><span data-stu-id="23053-112">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="23053-113">Фрагмент отрисовки представляет часть пользовательского интерфейса, подлежащую отрисовке.</span><span class="sxs-lookup"><span data-stu-id="23053-113">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="23053-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> принимает параметр типа, который можно задать при вызове фрагмента отрисовки.</span><span class="sxs-lookup"><span data-stu-id="23053-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="23053-115">Компонент `TableTemplate` (`TableTemplate.razor`):</span><span class="sxs-lookup"><span data-stu-id="23053-115">`TableTemplate` component (`TableTemplate.razor`):</span></span>

[!code-razor[](../common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="23053-116">При использовании шаблонного компонента параметры шаблона можно задать с помощью дочерних элементов, имена которых совпадают с именами параметров (`TableHeader` и `RowTemplate` в следующем примере):</span><span class="sxs-lookup"><span data-stu-id="23053-116">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { PetId = 2, Name = "Mr. Bigglesworth" },
        new Pet { PetId = 4, Name = "Salem Saberhagen" },
        new Pet { PetId = 7, Name = "K-9" }
    };

    private class Pet
    {
        public int PetId { get; set; }
        public string Name { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="23053-117">Ограничения универсального типа будут поддерживаться в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="23053-117">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="23053-118">Дополнительные сведения см. в разделе [Разрешение ограничений универсального типа (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="23053-118">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="23053-119">Параметры контекста шаблона</span><span class="sxs-lookup"><span data-stu-id="23053-119">Template context parameters</span></span>

<span data-ttu-id="23053-120">Аргументы компонента типа <xref:Microsoft.AspNetCore.Components.RenderFragment%601>, передаваемые как элементы, имеют неявный параметр `context` (например, `@context.PetId` в предыдущем примере кода), но его имя можно изменить с помощью атрибута `Context` дочернего элемента.</span><span class="sxs-lookup"><span data-stu-id="23053-120">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="23053-121">В следующем примере атрибут `Context` элемента `RowTemplate` задает имя параметра `pet`:</span><span class="sxs-lookup"><span data-stu-id="23053-121">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

@code {
    ...
}
```

<span data-ttu-id="23053-122">Кроме того, атрибут `Context` можно задать для элемента компонента.</span><span class="sxs-lookup"><span data-stu-id="23053-122">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="23053-123">Заданный атрибут `Context` применяется ко всем указанным параметрам шаблона.</span><span class="sxs-lookup"><span data-stu-id="23053-123">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="23053-124">Это может быть полезно, если необходимо указать имя параметра содержимого для неявного дочернего содержимого (без содержащего дочернего элемента).</span><span class="sxs-lookup"><span data-stu-id="23053-124">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="23053-125">В следующем примере элемент `TableTemplate` имеет атрибут `Context`, который применяется ко всем параметрам шаблона:</span><span class="sxs-lookup"><span data-stu-id="23053-125">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

@code {
    ...
}
```

## <a name="generic-typed-components"></a><span data-ttu-id="23053-126">Компоненты универсального типа</span><span class="sxs-lookup"><span data-stu-id="23053-126">Generic-typed components</span></span>

<span data-ttu-id="23053-127">Шаблонные компоненты часто имеют универсальный тип.</span><span class="sxs-lookup"><span data-stu-id="23053-127">Templated components are often generically typed.</span></span> <span data-ttu-id="23053-128">Например, универсальный компонент `ListViewTemplate` (`ListViewTemplate.razor`) можно использовать для отрисовки значений `IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="23053-128">For example, a generic `ListViewTemplate` component (`ListViewTemplate.razor`) can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="23053-129">Чтобы определить универсальный компонент, используйте директиву [`@typeparam`](xref:mvc/views/razor#typeparam) и укажите параметры типа:</span><span class="sxs-lookup"><span data-stu-id="23053-129">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](../common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="23053-130">При использовании компонентов универсального типа параметр типа выводится, если это возможно:</span><span class="sxs-lookup"><span data-stu-id="23053-130">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "Salem Saberhagen" },
        new Pet { Name = "K-9" }
    };

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="23053-131">В противном случае параметр типа необходимо указать явно с помощью атрибута, совпадающего с именем параметра типа.</span><span class="sxs-lookup"><span data-stu-id="23053-131">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="23053-132">В следующем примере `TItem="Pet"` задает тип:</span><span class="sxs-lookup"><span data-stu-id="23053-132">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    ...
}
```
