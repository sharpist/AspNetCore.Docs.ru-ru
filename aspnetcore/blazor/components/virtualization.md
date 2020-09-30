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
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>Виртуализация компонентов ASP.NET Core Blazor

Автор: [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)

Повысьте воспринимаемую производительность отрисовки компонентов с помощью встроенной поддержки виртуализации платформы Blazor. Виртуализация — это метод отображения только видимых в данный момент частей пользовательского интерфейса. Например, виртуализация удобна в случае, когда в приложении должен быть отрисован длинный список или таблица с большим количеством строк, и в любой конкретный момент времени должно быть видимым только подмножество элементов. Blazor предоставляет `Virtualize` компонент, который можно использовать для добавления виртуализации в компоненты приложения.

::: moniker range=">= aspnetcore-5.0"

Без виртуализации обычный список или компонент на основе таблиц может использовать цикл C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) для отрисовки каждого элемента в списке или каждой строки в таблице:

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

Если список содержит тысячи элементов, его отрисовка может занять много времени. При этом не исключена заметная задержка отображения пользовательского интерфейса.

Вместо отрисовки каждого элемента списка по отдельности замените цикл [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) компонентом `Virtualize` и укажите источник фиксированного элемента с помощью `Items`. Отрисовываются только элементы, видимые в данный момент:

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

Вместо указания контекста для компонента с помощью `Context` можно использовать значение `context` (`@context.{PROPERTY}`) в шаблоне содержимого элемента:

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

Компонент `Virtualize` вычисляет количество подлежащих отрисовке элементов на основе высоты контейнера и размера отображаемых элементов.

## <a name="item-provider-delegate"></a>Делегат поставщика элементов

Если вы не хотите загружать все элементы в память, можно указать метод делегата поставщика элементов для параметра `ItemsProvider` компонента, который асинхронно извлекает запрошенные элементы по запросу:

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

Поставщик элементов получает `ItemsProviderRequest`, который указывает необходимое количество элементов, начиная с заданного начального индекса. Затем поставщик элементов извлекает запрошенные элементы из базы данных или другой службы и возвращает их в виде `ItemsProviderResult<TItem>` вместе с количеством всех элементов. Поставщик элементов может извлекать элементы с каждым запросом или кэшировать их, чтобы они были доступны. Не пытайтесь использовать поставщик элементов и назначать коллекцию `Items` тому же компоненту `Virtualize`.

В следующем примере показана загрузка сотрудников из `EmployeeService`:

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

## <a name="placeholder"></a>Заполнитель

Поскольку запрос элементов из удаленного источника данных может занимать некоторое время, можно отрисовать заполнитель (`<Placeholder>...</Placeholder>`), пока не будут доступны данные элемента:

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

## <a name="item-size"></a>Размер элемента

Размер каждого элемента в пикселях можно задать с помощью `ItemSize` (по умолчанию: 50 пикс.):

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a>Количество элементов в нерабочей области

`OverscanCount` определяет количество дополнительных элементов, отрисовываемых до и после видимой области. Этот параметр позволяет уменьшить частоту отрисовки во время прокрутки. Однако более высокие значения приводят к отображению большего числа элементов на странице (по умолчанию: 3):

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Так, отрисовка сетки или списка с сотнями строк, содержащих компоненты, является ресурсоемкой задачей. Вы можете виртуализировать макет сетки или списка, чтобы в любой момент времени отрисовывалось только подмножество компонентов. Примером отрисовки подмножества компонентов могут служить следующие компоненты в [примере приложения `Virtualization` (в репозитории GitHub aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* Компонент `Virtualize` ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): компонент на C#, который реализует <xref:Microsoft.AspNetCore.Components.ComponentBase> для отрисовки набора строк данных по погоде при выполнении прокрутки пользователем;
* Компонент `FetchData` ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): использует компонент `Virtualize` для вывода 25 строк данных по погоде за раз.

::: moniker-end

## <a name="state-changes"></a>Изменения состояния

При внесении изменений в элементы, отрисовываемые компонентом `Virtualize`, вызовите метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для принудительной оценки и отрисовки компонента.
