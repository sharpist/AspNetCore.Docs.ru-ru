---
title: Виртуализация компонентов ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать виртуализацию компонентов в приложениях ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
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
uid: blazor/components/virtualization
ms.openlocfilehash: eafad420d72a974cc64ebfd6abb3eff2d73a115d
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805561"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="ac783-103">Виртуализация компонентов ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ac783-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="ac783-104">Автор: [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="ac783-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ac783-105">Повысьте воспринимаемую производительность отрисовки компонентов с помощью встроенной поддержки виртуализации платформы Blazor.</span><span class="sxs-lookup"><span data-stu-id="ac783-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="ac783-106">Виртуализация — это метод отображения только видимых в данный момент частей пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="ac783-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="ac783-107">Например, виртуализация удобна в случае, когда в приложении должен быть отрисован длинный список элементов и в любой конкретный момент времени должно быть видимым только подмножество элементов.</span><span class="sxs-lookup"><span data-stu-id="ac783-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="ac783-108">Blazor предоставляет `Virtualize` компонент, который можно использовать для добавления виртуализации в компоненты приложения.</span><span class="sxs-lookup"><span data-stu-id="ac783-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="ac783-109">Без виртуализации обычный список может использовать цикл C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) для отрисовки каждого элемента в списке:</span><span class="sxs-lookup"><span data-stu-id="ac783-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="ac783-110">Если список содержит тысячи элементов, его отрисовка может занять много времени.</span><span class="sxs-lookup"><span data-stu-id="ac783-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="ac783-111">При этом не исключена заметная задержка отображения пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="ac783-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="ac783-112">Вместо отрисовки каждого элемента списка по отдельности замените цикл [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) компонентом `Virtualize` и укажите источник фиксированного элемента с помощью `Items`.</span><span class="sxs-lookup"><span data-stu-id="ac783-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="ac783-113">Отрисовываются только элементы, видимые в данный момент:</span><span class="sxs-lookup"><span data-stu-id="ac783-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="ac783-114">Вместо указания контекста для компонента с помощью `Context` можно использовать значение `context` (`@context.{PROPERTY}`) в шаблоне содержимого элемента:</span><span class="sxs-lookup"><span data-stu-id="ac783-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="ac783-115">Компонент `Virtualize` вычисляет количество подлежащих отрисовке элементов на основе высоты контейнера и размера отображаемых элементов.</span><span class="sxs-lookup"><span data-stu-id="ac783-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="ac783-116">Содержимое элемента для компонента `Virtualize` может включать в себя следующее:</span><span class="sxs-lookup"><span data-stu-id="ac783-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="ac783-117">обычный код HTML и код Razor, как показано в предыдущем примере;</span><span class="sxs-lookup"><span data-stu-id="ac783-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="ac783-118">один или несколько компонентов Razor;</span><span class="sxs-lookup"><span data-stu-id="ac783-118">One or more Razor components.</span></span>
* <span data-ttu-id="ac783-119">сочетание компонентов HTML/Razor и Razor.</span><span class="sxs-lookup"><span data-stu-id="ac783-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="ac783-120">Делегат поставщика элементов</span><span class="sxs-lookup"><span data-stu-id="ac783-120">Item provider delegate</span></span>

<span data-ttu-id="ac783-121">Если вы не хотите загружать все элементы в память, можно указать метод делегата поставщика элементов для параметра `ItemsProvider` компонента, который асинхронно извлекает запрошенные элементы по запросу:</span><span class="sxs-lookup"><span data-stu-id="ac783-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="ac783-122">Поставщик элементов получает `ItemsProviderRequest`, который указывает необходимое количество элементов, начиная с заданного начального индекса.</span><span class="sxs-lookup"><span data-stu-id="ac783-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="ac783-123">Затем поставщик элементов извлекает запрошенные элементы из базы данных или другой службы и возвращает их в виде `ItemsProviderResult<TItem>` вместе с количеством всех элементов.</span><span class="sxs-lookup"><span data-stu-id="ac783-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="ac783-124">Поставщик элементов может извлекать элементы с каждым запросом или кэшировать их, чтобы они были доступны.</span><span class="sxs-lookup"><span data-stu-id="ac783-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="ac783-125">Не пытайтесь использовать поставщик элементов и назначать коллекцию `Items` тому же компоненту `Virtualize`.</span><span class="sxs-lookup"><span data-stu-id="ac783-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="ac783-126">В следующем примере показана загрузка сотрудников из `EmployeeService`:</span><span class="sxs-lookup"><span data-stu-id="ac783-126">The following example loads employees from an `EmployeeService`:</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a><span data-ttu-id="ac783-127">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="ac783-127">Placeholder</span></span>

<span data-ttu-id="ac783-128">Поскольку запрос элементов из удаленного источника данных может занимать некоторое время, можно отрисовать заполнитель (`<Placeholder>...</Placeholder>`), пока не будут доступны данные элемента:</span><span class="sxs-lookup"><span data-stu-id="ac783-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="ac783-129">Размер элемента</span><span class="sxs-lookup"><span data-stu-id="ac783-129">Item size</span></span>

<span data-ttu-id="ac783-130">Размер каждого элемента в пикселях можно задать с помощью `ItemSize` (по умолчанию: 50 пикс.):</span><span class="sxs-lookup"><span data-stu-id="ac783-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="ac783-131">Количество элементов в нерабочей области</span><span class="sxs-lookup"><span data-stu-id="ac783-131">Overscan count</span></span>

<span data-ttu-id="ac783-132">`OverscanCount` определяет количество дополнительных элементов, отрисовываемых до и после видимой области.</span><span class="sxs-lookup"><span data-stu-id="ac783-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="ac783-133">Этот параметр позволяет уменьшить частоту отрисовки во время прокрутки.</span><span class="sxs-lookup"><span data-stu-id="ac783-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="ac783-134">Однако более высокие значения приводят к отображению большего числа элементов на странице (по умолчанию: 3):</span><span class="sxs-lookup"><span data-stu-id="ac783-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="ac783-135">Изменения состояния</span><span class="sxs-lookup"><span data-stu-id="ac783-135">State changes</span></span>

<span data-ttu-id="ac783-136">При внесении изменений в элементы, отрисовываемые компонентом `Virtualize`, вызовите метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для принудительной оценки и отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="ac783-136">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
