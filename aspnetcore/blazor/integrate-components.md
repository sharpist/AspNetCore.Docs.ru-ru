---
title: Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages
author: guardrex
description: Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 97515ec519c4bedb0478f510ec9ed739b5d76e4f
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105250"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages

Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)

Компоненты Razor можно интегрировать в приложения MVC и Razor Pages. Одновременно с отрисовкой страницы или представления можно выполнять предварительную обработку компонентов.

После [подготовки приложения](#prepare-the-app) используйте рекомендации в следующих разделах в зависимости от требований приложения:

* Маршрутизируемые компоненты. Для компонентов, напрямую маршрутизируемых из запросов пользователей. Следуйте этому руководству, когда посетители должны иметь возможность сделать HTTP-запрос в браузере для компонента с директивой [`@page`](xref:mvc/views/razor#page).
  * [Использование маршрутизируемых компонентов в приложении Razor Pages](#use-routable-components-in-a-razor-pages-app)
  * [Использование маршрутизируемых компонентов в приложении MVC](#use-routable-components-in-an-mvc-app)
* [Отрисовка компонентов со страницы или представления.](#render-components-from-a-page-or-view) Для компонентов, которые не маршрутизируются напрямую из запросов пользователей. Следуйте этому руководству, когда приложение внедряет компоненты в существующие страницы и представления с помощью вспомогательной функции [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="prepare-the-app"></a>Подготовка приложения

Существующее приложение MVC или Razor Pages может интегрировать компоненты Razor в страницы и представления:

1. В файле макета приложения ( *_Layout.cshtml*) сделайте следующее:

   * Добавьте следующий тег `<base>` в элемент `<head>`:

     ```html
     <base href="~/" />
     ```

     Значение `href` (*базовый путь к приложению* ) в предыдущем примере предполагает, что приложение находится по корневому URL-пути (`/`). Если приложение является подчиненным, следуйте инструкциям в разделе *Базовый путь к приложению* статьи <xref:host-and-deploy/blazor/index#app-base-path>.

     Файл *_Layout.cshtml* находится в папке *Pages/Shared* приложения Razor Pages или папке *Views/Shared* приложения MVC.

   * Добавьте тег `<script>` для скрипта *blazor.server.js* непосредственно перед закрывающим тегом `</body>`:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Платформа добавляет скрипт *blazor.server.js* в приложение. Добавлять скрипт в приложение вручную не требуется.

1. Добавьте файл *_Imports.razor* в корневую папку проекта со следующим содержимым (измените последнее пространство имен `MyAppNamespace` на пространство имен приложения):

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. В `Startup.ConfigureServices` зарегистрируйте службу Blazor Server.

   ```csharp
   services.AddServerSideBlazor();
   ```

1. В `Startup.Configure` добавьте конечную точку Blazor Hub в `app.UseEndpoints`.

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Интегрируйте компоненты в какую-либо страницу или какое-либо представление. Дополнительные сведения см. в разделе [Отрисовка компонентов со страницы или представления](#render-components-from-a-page-or-view).

## <a name="use-routable-components-in-a-razor-pages-app"></a>Использование маршрутизируемых компонентов в приложении Razor Pages

*Этот раздел описывает добавление компонентов, напрямую маршрутизируемых из запросов пользователей.*

Чтобы обеспечить поддержку маршрутизируемых компонентов Razor в приложениях Razor Pages:

1. Следуйте указаниям в разделе [подготовка приложений](#prepare-the-app).

1. Добавьте файл *App.razor* в корневой каталог проекта со следующим содержимым:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Добавьте файл *_Host.cshtml* в папку *Pages* со следующим содержимым:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Для макета компоненты используют общий файл *_Layout.cshtml*.

   Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента `App`:

   * компонент предварительно преобразуется в страницу;
   * компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.

   | Режим обработки | Описание |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Преобразует компонент `App` в статический HTML и включает метку приложения Blazor Server. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Отображает метку приложения Blazor Server. Выходные данные компонента `App` не включаются. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Преобразует компонент `App` в статический HTML. |

   Дополнительные сведения о компоненте Tag Helper см. в разделе <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

1. Добавьте маршрут с низким приоритетом для страницы *_Host.cshtml* в конфигурацию конечной точки в `Startup.Configure`:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Добавьте маршрутизируемые компоненты в приложение. Пример:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Дополнительные сведения о пространствах имен см. в разделе [Пространства имен компонентов](#component-namespaces).

## <a name="use-routable-components-in-an-mvc-app"></a>Использование маршрутизируемых компонентов в приложении MVC

*Этот раздел описывает добавление компонентов, напрямую маршрутизируемых из запросов пользователей.*

Для поддержки маршрутизируемых компонентов Razor в приложениях MVC сделайте следующее:

1. Следуйте указаниям в разделе [подготовка приложений](#prepare-the-app).

1. Добавьте файл *App.razor* в корневой каталог проекта со следующим содержимым:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Добавьте файл *_Host.cshtml* в папку *Views/Home* со следующим содержимым:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Для макета компоненты используют общий файл *_Layout.cshtml*.
   
   Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента `App`:

   * компонент предварительно преобразуется в страницу;
   * компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.

   | Режим обработки | Описание |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Преобразует компонент `App` в статический HTML и включает метку приложения Blazor Server. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Отображает метку приложения Blazor Server. Выходные данные компонента `App` не включаются. При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Преобразует компонент `App` в статический HTML. |

   Дополнительные сведения о компоненте Tag Helper см. в разделе <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

1. Добавьте действие в контроллер Home:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Добавьте маршрут с низким приоритетом для действия контроллера, которое возвращает представление *_Host.cshtml*, в конфигурацию конечной точки в `Startup.Configure`:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Создайте папку *Pages* и добавьте маршрутизируемые компоненты в приложение. Пример:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Дополнительные сведения о пространствах имен см. в разделе [Пространства имен компонентов](#component-namespaces).

## <a name="render-components-from-a-page-or-view"></a>Отрисовка компонентов со страницы или представления

*Этот раздел описывает добавление на страницы или в представления компонентов, не являющихся напрямую маршрутизируемыми из запросов пользователей.*

Чтобы отрисовать компонент из страницы или представления, используйте [вспомогательную функцию тега компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

### <a name="render-stateful-interactive-components"></a>Отрисовка интерактивных компонентов с отслеживанием состояния

На страницу или в представление Razor можно добавить интерактивные компоненты с отслеживанием состояния.

При отображении страницы или представления:

* компонент предварительно отображается страницей или представлением;
* исходное состояние компонента, используемое для предварительной визуализации, теряется;
* новое состояние компонента создается при установке подключения SignalR.

Следующая страница Razor визуализирует компонент `Counter`.

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

### <a name="render-noninteractive-components"></a>Отрисовка неинтерактивных компонентов

На следующей странице Razor компонент `Counter` статически подготавливается к просмотру с начальным значением, указанным с помощью формы. Так как этот компонент отображается статически, он не может быть интерактивным:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="component-namespaces"></a>Пространства имен компонентов

При использовании настраиваемой папки для хранения компонентов приложения добавьте пространство имен, представляющее эту папку, на страницу или в представление либо в файл *_ViewImports.cshtml*. В следующем примере:

* Измените `MyAppNamespace` на пространство имен приложения.
* Если папка с именем *Components* не используется для хранения компонентов, измените `Components` на папку, где находятся компоненты.

```cshtml
@using MyAppNamespace.Components
```

Файл *_ViewImports.cshtml* находится в папке *Pages* приложения Razor Pages или папке *Views* приложения MVC.

Для получения дополнительной информации см. <xref:blazor/components#import-components>.
