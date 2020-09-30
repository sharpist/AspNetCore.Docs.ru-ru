---
title: Виртуализация компонентов ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать виртуализацию компонентов в приложениях ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/21/2020
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
ms.openlocfilehash: 911eeeb445741aa1519e1464dd4a75e26f6f12ab
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847576"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="e0cbf-103">Виртуализация компонентов ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e0cbf-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="e0cbf-104">Автор: [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="e0cbf-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="e0cbf-105">Повысьте воспринимаемую производительность отрисовки компонентов с помощью встроенной поддержки виртуализации платформы Blazor.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="e0cbf-106">Виртуализация — это метод отображения только видимых в данный момент частей пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="e0cbf-107">Например, виртуализация удобна в случае, когда в приложении должен быть отрисован длинный список или таблица с большим количеством строк, и в любой конкретный момент времени должно быть видимым только подмножество элементов.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-107">For example, virtualization is helpful when the app must render a long list or a table with many rows and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="e0cbf-108">Blazor предоставляет `Virtualize` компонент, который можно использовать для добавления виртуализации в компоненты приложения.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e0cbf-109">Без виртуализации обычный список или компонент на основе таблиц может использовать цикл C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) для отрисовки каждого элемента в списке или каждой строки в таблице:</span><span class="sxs-lookup"><span data-stu-id="e0cbf-109">Without virtualization, a typical list or table-based component might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list or each row in the table:</span></span>

```razor
<table>
    @foreach (var employee in employees)
    {
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    }
</table>
```

<span data-ttu-id="e0cbf-110">Если список содержит тысячи элементов, его отрисовка может занять много времени.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="e0cbf-111">При этом не исключена заметная задержка отображения пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="e0cbf-112">Вместо отрисовки каждого элемента списка по отдельности замените цикл [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) компонентом `Virtualize` и укажите источник фиксированного элемента с помощью `Items`.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="e0cbf-113">Отрисовываются только элементы, видимые в данный момент:</span><span class="sxs-lookup"><span data-stu-id="e0cbf-113">Only the items that are currently visible are rendered:</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees">
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="e0cbf-114">Вместо указания контекста для компонента с помощью `Context` можно использовать значение `context` (`@context.{PROPERTY}`) в шаблоне содержимого элемента:</span><span class="sxs-lookup"><span data-stu-id="e0cbf-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<table>
    <Virtualize Items="@employees">
        <tr>
            <td>@context.FirstName</td>
            <td>@context.LastName</td>
            <td>@context.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="e0cbf-115">Компонент `Virtualize` вычисляет количество подлежащих отрисовке элементов на основе высоты контейнера и размера отображаемых элементов.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="e0cbf-116">Делегат поставщика элементов</span><span class="sxs-lookup"><span data-stu-id="e0cbf-116">Item provider delegate</span></span>

<span data-ttu-id="e0cbf-117">Если вы не хотите загружать все элементы в память, можно указать метод делегата поставщика элементов для параметра `ItemsProvider` компонента, который асинхронно извлекает запрошенные элементы по запросу:</span><span class="sxs-lookup"><span data-stu-id="e0cbf-117">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
         <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="e0cbf-118">Поставщик элементов получает `ItemsProviderRequest`, который указывает необходимое количество элементов, начиная с заданного начального индекса.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-118">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="e0cbf-119">Затем поставщик элементов извлекает запрошенные элементы из базы данных или другой службы и возвращает их в виде `ItemsProviderResult<TItem>` вместе с количеством всех элементов.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-119">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="e0cbf-120">Поставщик элементов может извлекать элементы с каждым запросом или кэшировать их, чтобы они были доступны.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-120">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="e0cbf-121">Не пытайтесь использовать поставщик элементов и назначать коллекцию `Items` тому же компоненту `Virtualize`.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-121">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="e0cbf-122">В следующем примере показана загрузка сотрудников из `EmployeeService`:</span><span class="sxs-lookup"><span data-stu-id="e0cbf-122">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="e0cbf-123">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="e0cbf-123">Placeholder</span></span>

<span data-ttu-id="e0cbf-124">Поскольку запрос элементов из удаленного источника данных может занимать некоторое время, можно отрисовать заполнитель (`<Placeholder>...</Placeholder>`), пока не будут доступны данные элемента:</span><span class="sxs-lookup"><span data-stu-id="e0cbf-124">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
        <ItemContent>
            <tr>
                <td>@employee.FirstName</td>
                <td>@employee.LastName</td>
                <td>@employee.JobTitle</td>
            </tr>
        </ItemContent>
        <Placeholder>
            <tr>
                <td>Loading...</td>
            </tr>
        </Placeholder>
    </Virtualize>
</table>
```

## <a name="item-size"></a><span data-ttu-id="e0cbf-125">Размер элемента</span><span class="sxs-lookup"><span data-stu-id="e0cbf-125">Item size</span></span>

<span data-ttu-id="e0cbf-126">Размер каждого элемента в пикселях можно задать с помощью `ItemSize` (по умолчанию: 50 пикс.):</span><span class="sxs-lookup"><span data-stu-id="e0cbf-126">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a><span data-ttu-id="e0cbf-127">Количество элементов в нерабочей области</span><span class="sxs-lookup"><span data-stu-id="e0cbf-127">Overscan count</span></span>

<span data-ttu-id="e0cbf-128">`OverscanCount` определяет количество дополнительных элементов, отрисовываемых до и после видимой области.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-128">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="e0cbf-129">Этот параметр позволяет уменьшить частоту отрисовки во время прокрутки.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-129">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="e0cbf-130">Однако более высокие значения приводят к отображению большего числа элементов на странице (по умолчанию: 3):</span><span class="sxs-lookup"><span data-stu-id="e0cbf-130">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e0cbf-131">Так, отрисовка сетки или списка с сотнями строк, содержащих компоненты, является ресурсоемкой задачей.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-131">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="e0cbf-132">Вы можете виртуализировать макет сетки или списка, чтобы в любой момент времени отрисовывалось только подмножество компонентов.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-132">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="e0cbf-133">Примером отрисовки подмножества компонентов могут служить следующие компоненты в [примере приложения `Virtualization` (в репозитории GitHub aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="e0cbf-133">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="e0cbf-134">Компонент `Virtualize` ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): компонент на C#, который реализует <xref:Microsoft.AspNetCore.Components.ComponentBase> для отрисовки набора строк данных по погоде при выполнении прокрутки пользователем;</span><span class="sxs-lookup"><span data-stu-id="e0cbf-134">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="e0cbf-135">Компонент `FetchData` ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): использует компонент `Virtualize` для вывода 25 строк данных по погоде за раз.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-135">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="e0cbf-136">Изменения состояния</span><span class="sxs-lookup"><span data-stu-id="e0cbf-136">State changes</span></span>

<span data-ttu-id="e0cbf-137">При внесении изменений в элементы, отрисовываемые компонентом `Virtualize`, вызовите метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для принудительной оценки и отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="e0cbf-137">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
