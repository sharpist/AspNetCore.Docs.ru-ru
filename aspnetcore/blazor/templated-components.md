---
<span data-ttu-id="1a45e-101">title: 'Шаблонные компоненты ASP.NET Core Blazor' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1a45e-101">title: 'ASP.NET Core Blazor templated components' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a45e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a45e-102">'Blazor'</span></span>
- <span data-ttu-id="1a45e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a45e-103">'Identity'</span></span>
- <span data-ttu-id="1a45e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a45e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a45e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a45e-105">'Razor'</span></span>
- <span data-ttu-id="1a45e-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="1a45e-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="1a45e-107">Шаблонные компоненты Blazor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a45e-107">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="1a45e-108">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="1a45e-108">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="1a45e-109">Шаблонные компоненты — это компоненты, которые могут принимать один или несколько шаблонов пользовательского интерфейса в качестве параметров, используемых затем как часть логики отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="1a45e-109">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="1a45e-110">Шаблонные компоненты позволяют разрабатывать более общие компоненты, которые удобнее использовать повторно, чем обычные.</span><span class="sxs-lookup"><span data-stu-id="1a45e-110">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="1a45e-111">Вот несколько примеров:</span><span class="sxs-lookup"><span data-stu-id="1a45e-111">A couple of examples include:</span></span>

* <span data-ttu-id="1a45e-112">компонент таблицы, позволяющий пользователю задавать шаблоны для заголовка, строк и нижнего колонтитула таблицы;</span><span class="sxs-lookup"><span data-stu-id="1a45e-112">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="1a45e-113">компонент списка, позволяющий пользователю задавать шаблон для отрисовываемых элементов списка.</span><span class="sxs-lookup"><span data-stu-id="1a45e-113">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="1a45e-114">Параметры шаблона</span><span class="sxs-lookup"><span data-stu-id="1a45e-114">Template parameters</span></span>

<span data-ttu-id="1a45e-115">Шаблонный компонент определяется путем указания одного или нескольких параметров компонента типа <xref:Microsoft.AspNetCore.Components.RenderFragment> или <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span><span class="sxs-lookup"><span data-stu-id="1a45e-115">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="1a45e-116">Фрагмент отрисовки представляет часть пользовательского интерфейса, подлежащую отрисовке.</span><span class="sxs-lookup"><span data-stu-id="1a45e-116">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="1a45e-117"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> принимает параметр типа, который можно задать при вызове фрагмента отрисовки.</span><span class="sxs-lookup"><span data-stu-id="1a45e-117"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="1a45e-118">Компонент `TableTemplate`:</span><span class="sxs-lookup"><span data-stu-id="1a45e-118">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="1a45e-119">При использовании шаблонного компонента параметры шаблона можно задать с помощью дочерних элементов, имена которых совпадают с именами параметров (`TableHeader` и `RowTemplate` в следующем примере):</span><span class="sxs-lookup"><span data-stu-id="1a45e-119">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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
> <span data-ttu-id="1a45e-120">Ограничения универсального типа будут поддерживаться в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="1a45e-120">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="1a45e-121">Дополнительные сведения см. в разделе [Разрешение ограничений универсального типа (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="1a45e-121">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="1a45e-122">Параметры контекста шаблона</span><span class="sxs-lookup"><span data-stu-id="1a45e-122">Template context parameters</span></span>

<span data-ttu-id="1a45e-123">Аргументы компонента типа <xref:Microsoft.AspNetCore.Components.RenderFragment%601>, передаваемые как элементы, имеют неявный параметр `context` (например, `@context.PetId` в предыдущем примере кода), но его имя можно изменить с помощью атрибута `Context` дочернего элемента.</span><span class="sxs-lookup"><span data-stu-id="1a45e-123">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="1a45e-124">В следующем примере атрибут `Context` элемента `RowTemplate` задает имя параметра `pet`:</span><span class="sxs-lookup"><span data-stu-id="1a45e-124">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="1a45e-125">Кроме того, атрибут `Context` можно задать для элемента компонента.</span><span class="sxs-lookup"><span data-stu-id="1a45e-125">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="1a45e-126">Заданный атрибут `Context` применяется ко всем указанным параметрам шаблона.</span><span class="sxs-lookup"><span data-stu-id="1a45e-126">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="1a45e-127">Это может быть полезно, если необходимо указать имя параметра содержимого для неявного дочернего содержимого (без содержащего дочернего элемента).</span><span class="sxs-lookup"><span data-stu-id="1a45e-127">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="1a45e-128">В следующем примере элемент `TableTemplate` имеет атрибут `Context`, который применяется ко всем параметрам шаблона:</span><span class="sxs-lookup"><span data-stu-id="1a45e-128">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

## <a name="generic-typed-components"></a><span data-ttu-id="1a45e-129">Компоненты универсального типа</span><span class="sxs-lookup"><span data-stu-id="1a45e-129">Generic-typed components</span></span>

<span data-ttu-id="1a45e-130">Шаблонные компоненты часто имеют универсальный тип.</span><span class="sxs-lookup"><span data-stu-id="1a45e-130">Templated components are often generically typed.</span></span> <span data-ttu-id="1a45e-131">Например, универсальный компонент `ListViewTemplate` можно использовать для отрисовки значений `IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="1a45e-131">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="1a45e-132">Чтобы определить универсальный компонент, используйте директиву [`@typeparam`](xref:mvc/views/razor#typeparam) и укажите параметры типа:</span><span class="sxs-lookup"><span data-stu-id="1a45e-132">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="1a45e-133">При использовании компонентов универсального типа параметр типа выводится, если это возможно:</span><span class="sxs-lookup"><span data-stu-id="1a45e-133">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="1a45e-134">В противном случае параметр типа необходимо указать явно с помощью атрибута, совпадающего с именем параметра типа.</span><span class="sxs-lookup"><span data-stu-id="1a45e-134">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="1a45e-135">В следующем примере `TItem="Pet"` задает тип:</span><span class="sxs-lookup"><span data-stu-id="1a45e-135">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
