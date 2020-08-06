---
title: Маршрутизация ASP.NET Core Blazor
author: guardrex
description: Узнайте, как маршрутизировать запросы в приложениях и использовать компонент NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: f7110688a7a03dd0cf533fa5f9759af3363169f8
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818941"
---
# <a name="aspnet-core-no-locblazor-routing"></a>Маршрутизация ASP.NET Core Blazor

Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)

Узнайте, как маршрутизировать запросы и использовать компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> для создания навигационных ссылок в приложениях Blazor.

## <a name="aspnet-core-endpoint-routing-integration"></a>Интеграция маршрутизации конечных точек ASP.NET Core

Blazor Server интегрирован с функцией [маршрутизации конечных точек ASP.NET Core](xref:fundamentals/routing). Приложение ASP.NET Core настроено для приема входящих подключений для интерактивных компонентов с помощью <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> в `Startup.Configure`.

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

Наиболее типичная конфигурация — маршрутизация всех запросов на страницу Razor, которая выступает в качестве узла для серверной части приложения Blazor Server. По соглашению страница *узла* обычно называется `_Host.cshtml`. Маршрут, указанный в файле узла, называется *резервным маршрутом*, так как он работает с низким приоритетом в соответствии с правилами маршрутизации. Резервный маршрут рассматривается, если другие маршруты не сопоставляются. Это позволяет приложению использовать другие контроллеры и страницы, не мешая работе приложения Blazor Server.

Сведения о настройке <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> для размещения сервера по некорневому URL-адресу см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="route-templates"></a>Шаблоны маршрутов

Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> позволяет выполнять маршрутизацию для каждого компонента с указанным маршрутом. Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> находится в файле `App.razor`.

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

При компиляции файла `.razor` с директивой `@page` созданный класс предоставляет атрибут <xref:Microsoft.AspNetCore.Components.RouteAttribute> для указания шаблона маршрута.

В среде выполнения компонент <xref:Microsoft.AspNetCore.Components.RouteView> выполняет следующие операции:

* получает <xref:Microsoft.AspNetCore.Components.RouteData> от <xref:Microsoft.AspNetCore.Components.Routing.Router> вместе с всеми необходимыми параметрами;
* визуализирует указанный компонент с его макетом (или необязательным макетом по умолчанию), используя указанные параметры.

При необходимости можно указать параметр <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> с классом макета, который будет использоваться для компонентов, которые не задают макет. Шаблоны Blazor по умолчанию определяют компонент `MainLayout`. Файл `MainLayout.razor` находится в папке `Shared` проекта шаблона. Дополнительные сведения о макетах см. в разделе <xref:blazor/layouts>.

К компоненту можно применить несколько шаблонов маршрутов. Следующий компонент отвечает на запросы `/BlazorRoute` и `/DifferentBlazorRoute`.

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Для правильного разрешения URL-адресов приложение должно содержать тег `<base>` в файле `wwwroot/index.html` (Blazor WebAssembly) или файле `Pages/_Host.cshtml` (Blazor Server) с базовым путем к приложению, указанным в атрибуте `href` (`<base href="/">`). Для получения дополнительной информации см. <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Предоставление пользовательского содержимого, когда содержимое не найдено

Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> позволяет приложению указать пользовательское содержимое, если содержимое для запрошенного маршрута не найдено.

В файле `App.razor` задайте пользовательское содержимое в параметре шаблона <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>.

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

Содержимое тегов `<NotFound>` может включать произвольные элементы, например другие интерактивные компоненты. Сведения о применении макета по умолчанию к содержимому <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> см. в разделе <xref:blazor/layouts>.

## <a name="route-to-components-from-multiple-assemblies"></a>Маршрутизация к компонентам из нескольких сборок

Используйте параметр <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>, чтобы указать дополнительные сборки для компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>, которые следует учитывать при поиске маршрутизируемых компонентов. Указанные сборки рассматриваются в дополнение к сборке, указанной в `AppAssembly`. В следующем примере `Component1` представляет собой маршрутизируемый компонент, определенный в упоминаемой библиотеке классов. В следующем примере <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> приводится поддержка маршрутизации для `Component1`.

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Параметры маршрута

Маршрутизатор использует параметры маршрута для заполнения соответствующих параметров компонента с тем же именем (без учета регистра).

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

Необязательные параметры не поддерживаются. В предыдущем примере применяются две директивы `@page`. Первая позволяет переходить к компоненту без параметра. Вторая директива `@page` принимает параметр маршрута `{text}` и присваивает значение свойству `Text`.

## <a name="route-constraints"></a>Ограничения маршрута

Ограничение маршрута применяет сопоставление типов в сегменте маршрута к компоненту.

В следующем примере маршрут к компоненту `Users` соответствует только в следующих случаях:

* в URL-адресе запроса имеется сегмент маршрута `Id`;
* сегмент `Id` является целым числом (`int`).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Доступны ограничения маршрутов, приведенные в следующей таблице. Сведения об ограничениях маршрута, соответствующих инвариантному языку и региональным параметрам, см. в предупреждении внизу таблицы.

| Ограничение | Пример           | Примеры совпадений                                                                  | Инвариант<br>язык и региональные параметры<br>соответствие |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Нет                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Да                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Да                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Да                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Да                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Нет                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Да                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Да                              |

> [!WARNING]
> Ограничения маршрута, которые проверяют URL-адрес и могут быть преобразованы в тип CLR (например, `int` или <xref:System.DateTime>), всегда используют инвариантные язык и региональные параметры. Эти ограничения предполагают, что URL-адрес является нелокализуемым.

### <a name="routing-with-urls-that-contain-dots"></a>Маршрутизация URL-адресов, содержащих точки

В приложениях Blazor Server маршрутом по умолчанию в `_Host.cshtml` является `/` (`@page "/"`). URL-адрес запроса, содержащий точку (`.`), не соответствует маршруту по умолчанию, так как такой адрес считается запросом файла. Приложение Blazor возвращает ответ *404 Not Found* (Не найдено) для статического файла, который не существует. Чтобы использовать маршруты, содержащие точку, добавьте в файл `_Host.cshtml` следующий шаблон маршрута:

```cshtml
@page "/{**path}"
```

Шаблон `"/{**path}"` включает в себя следующие элементы:

* двойная *универсальная* звездочка (`**`) для захвата пути через границы нескольких папок без кодирования косой чертой (`/`);
* имя параметра маршрута `path`.

> [!NOTE]
> Синтаксис *универсального* параметра (`*`/`**`) **не** поддерживается в компонентах Razor (`.razor`).

Для получения дополнительной информации см. <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Компонент NavLink

Используйте при создании ссылок навигации компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> вместо HTML-элементов гиперссылок (`<a>`). Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ведет себя как элемент `<a>`, за исключением того, что он переключает класс CSS `active` в зависимости от того, соответствует ли его `href` текущему URL-адресу. Класс `active` помогает пользователю понять, какая страница является активной страницей среди отображаемых ссылок навигации. При необходимости назначьте имя класса CSS свойству <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType>, чтобы применить пользовательский класс CSS к отображаемой ссылке, если текущий маршрут совпадает с `href`.

Следующий компонент `NavMenu` создает панель навигации [`Bootstrap`](https://getbootstrap.com/docs/), которая демонстрирует использование компонентов <xref:Microsoft.AspNetCore.Components.Routing.NavLink>:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Существует два параметра <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>, которые можно назначить атрибуту `Match` элемента `<NavLink>`:

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> активен, если он соответствует всему текущему URL-адресу;
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*по умолчанию*). <xref:Microsoft.AspNetCore.Components.Routing.NavLink> активен, если он соответствует любому префиксу текущего URL-адреса.

В предыдущем примере <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` элемента Home соответствует домашнему URL-адресу и получает только класс CSS `active` в URL-адресе базового пути приложения по умолчанию (например, `https://localhost:5001/`). Второй <xref:Microsoft.AspNetCore.Components.Routing.NavLink> получает класс `active`, когда пользователь посещает любой URL-адрес с префиксом `MyComponent` (например, `https://localhost:5001/MyComponent` и `https://localhost:5001/MyComponent/AnotherSegment`).

Дополнительные атрибуты компонента <xref:Microsoft.AspNetCore.Components.Routing.NavLink> передаются в отображаемый тег привязки. В следующем примере компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> включает атрибут `target`.

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Отобразится следующая разметка HTML.

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> В связи с тем, как Blazor выполняет рендеринг дочернего содержимого, для рендеринга компонентов `NavLink` в цикле `for` требуется задать локальную переменную индекса, если в содержимом дочернего компонента `NavLink` используется переменная цикла приращения:
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> Использование переменной индекса в этом сценарии обязательно для **любого** дочернего компонента, который использует переменную цикла в своем [дочернем содержимом](xref:blazor/components/index#child-content), а не только для компонента `NavLink`.
>
> Вместо этого можно использовать цикл `foreach` с <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a>URI и вспомогательные инструменты состояния навигации

Используйте <xref:Microsoft.AspNetCore.Components.NavigationManager> для работы с URI и навигацией в коде C#. <xref:Microsoft.AspNetCore.Components.NavigationManager> предоставляет события и методы, приведенные в следующей таблице.

| Член | Описание |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Возвращает текущий абсолютный URI. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Получает базовый URI (с завершающей косой чертой), который можно добавить в начало относительных путей URI для получения абсолютного URI. Как правило, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> соответствует атрибуту `href` элемента документа `<base>` в `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server). |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Переходит по указанному URI. Если значение `forceLoad` равно `true`:<ul><li>маршрутизация на стороне клиента обходится;</li><li>браузер принуждается к загрузке новой страницы с сервера, независимо от того, обрабатывается ли универсальный код ресурса клиентским маршрутизатором.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Событие, возникающее при изменении расположения навигации. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Преобразует относительный URI в абсолютный. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Если указан базовый URI (например, URI, возвращенный ранее <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), преобразует абсолютный URI в URI относительно базового префикса. |

Следующий компонент при нажатии кнопки переходит к компоненту приложения `Counter`.

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

Следующий компонент обрабатывает событие изменения расположения путем оформления подписки на <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>. При вызове `Dispose` платформой метод `HandleLocationChanged` отсоединяется. После отсоединения метода становится возможной сборка мусора для компонента.

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

В <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> содержатся следующие сведения о событии.

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>. URL-адрес нового расположения.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>. Если `true`, Blazor перехватывает навигацию из браузера. Если `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> приводит к переходу.

Дополнительные сведения об удалении компонентов см. в разделе <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

## <a name="query-string-and-parse-parameters"></a>Строка запроса и параметры анализа

Строку запроса можно получить из свойства <xref:Microsoft.AspNetCore.Components.NavigationManager> <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri>:

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

Чтобы проанализировать параметры строки запроса:

* Добавьте ссылку на пакет для [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).
* Получите значение после анализа строки запроса с помощью <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

Заполнитель `{KEY}` в предыдущем примере — это ключ параметра строки запроса. Например, пара "ключ-значение" URL-адреса `?ship=Tardis` использует ключ `ship`.
