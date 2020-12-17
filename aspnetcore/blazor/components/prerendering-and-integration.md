---
title: Компоненты Razor для предварительной визуализации и интеграции ASP.NET Core
author: guardrex
description: Сведения о сценариях интеграции компонентов Razor для приложений Blazor, в том числе о предварительной обработке компонентов Razor на сервере.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3402117334548f9d90880d4f536e8baa288e7bc9
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506985"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a><span data-ttu-id="0da1e-103">Компоненты Razor для предварительной визуализации и интеграции ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0da1e-103">Prerender and integrate ASP.NET Core Razor components</span></span>

<span data-ttu-id="0da1e-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="0da1e-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0da1e-105">Компоненты Razor можно интегрировать в приложения Razor Pages и MVC в размещенном решении Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="0da1e-105">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="0da1e-106">Одновременно с отрисовкой страницы или представления можно выполнять предварительную обработку компонентов.</span><span class="sxs-lookup"><span data-stu-id="0da1e-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="0da1e-107">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="0da1e-107">Configuration</span></span>

<span data-ttu-id="0da1e-108">Чтобы настроить предварительную визуализацию для приложения Blazor WebAssembly, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="0da1e-108">To set up prerendering for a Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="0da1e-109">Разместите приложение Blazor WebAssembly в приложении ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0da1e-109">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="0da1e-110">Вы можете добавить изолированное приложение Blazor WebAssembly в решение ASP.NET Core или использовать размещенное приложение Blazor WebAssembly, созданное из шаблона проекта Blazor Hosted.</span><span class="sxs-lookup"><span data-stu-id="0da1e-110">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the Blazor Hosted project template.</span></span>

1. <span data-ttu-id="0da1e-111">Удалите стандартный статический файл `wwwroot/index.html` из проекта клиента Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="0da1e-111">Remove the default static `wwwroot/index.html` file from the Blazor WebAssembly client project.</span></span>

1. <span data-ttu-id="0da1e-112">Удалите следующую строку в `Program.Main` в клиентском проекте:</span><span class="sxs-lookup"><span data-stu-id="0da1e-112">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="0da1e-113">Добавьте файл `Pages/_Host.cshtml` в серверный проект.</span><span class="sxs-lookup"><span data-stu-id="0da1e-113">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="0da1e-114">Файл `_Host.cshtml` можно получить из приложения, созданного из шаблона Blazor Server, используя в командной оболочке команду `dotnet new blazorserver -o BlazorServer`.</span><span class="sxs-lookup"><span data-stu-id="0da1e-114">You can obtain a `_Host.cshtml` file from an app created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell.</span></span> <span data-ttu-id="0da1e-115">После помещения файла `Pages/_Host.cshtml` в серверное приложение размещенного решения Blazor WebAssembly внесите следующие изменения в файл:</span><span class="sxs-lookup"><span data-stu-id="0da1e-115">After placing the `Pages/_Host.cshtml` file into the server app of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="0da1e-116">Задайте для пространства имен папку `Pages` серверного приложения (например, `@namespace BlazorHosted.Server.Pages`).</span><span class="sxs-lookup"><span data-stu-id="0da1e-116">Set the namespace to the server app's `Pages` folder (for example, `@namespace BlazorHosted.Server.Pages`).</span></span>
   * <span data-ttu-id="0da1e-117">Задайте директиву [`@using`](xref:mvc/views/razor#using) для клиентского проекта (например, `@using BlazorHosted.Client`).</span><span class="sxs-lookup"><span data-stu-id="0da1e-117">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="0da1e-118">Обновите ссылки таблицы стилей, чтобы они указывали на таблицу стилей приложения WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="0da1e-118">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="0da1e-119">В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-119">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="0da1e-120">Обновите `render-mode` [вспомогательной функции тегов компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper), чтобы выполнить предварительную визуализацию для корневого компонента `App`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-120">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="0da1e-121">Обновите источник скрипта Blazor, чтобы использовать скрипт Blazor WebAssembly на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="0da1e-121">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="0da1e-122">В `Startup.Configure` (`Startup.cs`) серверного проекта:</span><span class="sxs-lookup"><span data-stu-id="0da1e-122">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="0da1e-123">Вызовите `UseDeveloperExceptionPage` в конструкторе приложений в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="0da1e-123">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="0da1e-124">Вызовите `UseBlazorFrameworkFiles` в конструкторе приложений.</span><span class="sxs-lookup"><span data-stu-id="0da1e-124">Call `UseBlazorFrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="0da1e-125">Измените значение отката со страницы `index.html` (`endpoints.MapFallbackToFile("index.html");`) на страницу `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="0da1e-125">Change the fallback from the `index.html` page (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page.</span></span>

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="0da1e-126">Преобразование для просмотра компонентов на странице или в представлении с помощью вспомогательной функции тегов компонента</span><span class="sxs-lookup"><span data-stu-id="0da1e-126">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="0da1e-127">Вспомогательная функция тегов компонента поддерживает два режима отрисовки компонента из приложения Blazor WebAssembly на странице или в представлении:</span><span class="sxs-lookup"><span data-stu-id="0da1e-127">The Component Tag Helper supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <span data-ttu-id="0da1e-128">`WebAssembly`: Преобразовывает маркер для приложения Blazor WebAssembly, которое используется для включения интерактивного компонента при загрузке в браузере.</span><span class="sxs-lookup"><span data-stu-id="0da1e-128">`WebAssembly`: Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="0da1e-129">Компонент предварительно не визуализирован.</span><span class="sxs-lookup"><span data-stu-id="0da1e-129">The component isn't prerendered.</span></span> <span data-ttu-id="0da1e-130">Этот параметр упрощает преобразование различных компонентов Blazor WebAssembly на разных страницах.</span><span class="sxs-lookup"><span data-stu-id="0da1e-130">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span>
* <span data-ttu-id="0da1e-131">`WebAssemblyPrerendered`: Предварительно визуализирует компонент в статический HTML и включает маркер для приложения Blazor WebAssembly, который впоследствии будет использован, чтобы сделать компонент интерактивным при загрузке в браузере.</span><span class="sxs-lookup"><span data-stu-id="0da1e-131">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="0da1e-132">В следующем примере Razor Pages компонент `Counter` отрисовывается на странице.</span><span class="sxs-lookup"><span data-stu-id="0da1e-132">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="0da1e-133">Чтобы сделать компонент интерактивным, сценарий Blazor WebAssembly добавляется в раздел [отображения](xref:mvc/views/layout#sections) страницы.</span><span class="sxs-lookup"><span data-stu-id="0da1e-133">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="0da1e-134">Чтобы избежать использования полного пространства имен для компонента `Counter` с вспомогательной функцией тегов компонента (`{APP ASSEMBLY}.Pages.Counter`), добавьте директиву [`@using`](xref:mvc/views/razor#using) для пространства имен `Pages` приложения клиента.</span><span class="sxs-lookup"><span data-stu-id="0da1e-134">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="0da1e-135">В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-135">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="0da1e-136">Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента:</span><span class="sxs-lookup"><span data-stu-id="0da1e-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="0da1e-137">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="0da1e-137">Is prerendered into the page.</span></span>
* <span data-ttu-id="0da1e-138">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="0da1e-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="0da1e-139">Дополнительные сведения о вспомогательной функции тегов компонента, в том числе о передаче параметров и конфигурации <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, см. в статье <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="0da1e-139">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="0da1e-140">В предыдущем примере требуется, чтобы макет серверного приложения (`_Layout.cshtml`) содержал [раздел преобразования для просмотра](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) для скрипта в закрывающем теге `</body>`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-140">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="0da1e-141">Файл `_Layout.cshtml` находится в папке `Pages/Shared` приложения Razor Pages или в папке `Views/Shared` приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="0da1e-141">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="0da1e-142">Если приложение также должно иметь стиль компонентов со стилями в приложении Blazor WebAssembly, добавьте стили приложения в файл `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="0da1e-142">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="0da1e-143">В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-143">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="0da1e-144">Преобразование компонентов на странице или в представлении с помощью селектора CSS</span><span class="sxs-lookup"><span data-stu-id="0da1e-144">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="0da1e-145">Добавьте корневые компоненты в проект *Client* в `Program.Main` (`Program.cs`).</span><span class="sxs-lookup"><span data-stu-id="0da1e-145">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="0da1e-146">В следующем примере компонент `Counter` объявляется как корневой компонент с помощью селектора CSS, который выбирает элемент с `id`, соответствующим `my-counter`.</span><span class="sxs-lookup"><span data-stu-id="0da1e-146">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="0da1e-147">В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-147">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="0da1e-148">В следующем примере Razor Pages компонент `Counter` отрисовывается на странице.</span><span class="sxs-lookup"><span data-stu-id="0da1e-148">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="0da1e-149">Чтобы сделать компонент интерактивным, сценарий Blazor WebAssembly добавляется в раздел [отображения](xref:mvc/views/layout#sections) страницы:</span><span class="sxs-lookup"><span data-stu-id="0da1e-149">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="0da1e-150">В предыдущем примере требуется, чтобы макет серверного приложения (`_Layout.cshtml`) содержал [раздел преобразования для просмотра](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) для скрипта в закрывающем теге `</body>`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-150">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="0da1e-151">Файл `_Layout.cshtml` находится в папке `Pages/Shared` приложения Razor Pages или в папке `Views/Shared` приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="0da1e-151">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="0da1e-152">Если приложение также должно иметь стиль компонентов со стилями в приложении Blazor WebAssembly, добавьте стили приложения в файл `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="0da1e-152">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="0da1e-153">В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-153">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="0da1e-154">Интеграция компонентов Razor в приложения Razor Pages и MVC в размещенном решении Blazor WebAssembly поддерживается в ASP.NET Core в .NET 5 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="0da1e-154">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="0da1e-155">Выберите вариант этой статьи для версии .NET 5 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="0da1e-155">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="0da1e-156">Компоненты Razor можно интегрировать в приложения Razor Pages и MVC в приложении Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="0da1e-156">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="0da1e-157">Одновременно с отрисовкой страницы или представления можно выполнять предварительную обработку компонентов.</span><span class="sxs-lookup"><span data-stu-id="0da1e-157">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="0da1e-158">[Настроив приложение](#configuration), следуйте рекомендациям в следующих разделах в зависимости от требований приложения:</span><span class="sxs-lookup"><span data-stu-id="0da1e-158">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="0da1e-159">Маршрутизируемые компоненты. Для компонентов, напрямую маршрутизируемых из запросов пользователей.</span><span class="sxs-lookup"><span data-stu-id="0da1e-159">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="0da1e-160">Следуйте этому руководству, когда посетители должны иметь возможность сделать HTTP-запрос в браузере для компонента с директивой [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="0da1e-160">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="0da1e-161">Использование маршрутизируемых компонентов в приложении Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0da1e-161">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="0da1e-162">Использование маршрутизируемых компонентов в приложении MVC</span><span class="sxs-lookup"><span data-stu-id="0da1e-162">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="0da1e-163">[Отрисовка компонентов со страницы или представления.](#render-components-from-a-page-or-view) Для компонентов, которые не маршрутизируются напрямую из запросов пользователей.</span><span class="sxs-lookup"><span data-stu-id="0da1e-163">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="0da1e-164">Следуйте этому руководству, когда приложение внедряет компоненты в существующие страницы и представления с помощью вспомогательной функции [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="0da1e-164">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="0da1e-165">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="0da1e-165">Configuration</span></span>

<span data-ttu-id="0da1e-166">Существующее приложение MVC или Razor Pages может интегрировать компоненты Razor в страницы и представления:</span><span class="sxs-lookup"><span data-stu-id="0da1e-166">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="0da1e-167">В файле макета приложения (`_Layout.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="0da1e-167">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="0da1e-168">Добавьте следующий тег `<base>` в элемент `<head>`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-168">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="0da1e-169">Значение `href` (*базовый путь к приложению* ) в предыдущем примере предполагает, что приложение находится по корневому URL-пути (`/`).</span><span class="sxs-lookup"><span data-stu-id="0da1e-169">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="0da1e-170">Если приложение является подчиненным, следуйте инструкциям в разделе *Базовый путь к приложению* статьи <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="0da1e-170">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="0da1e-171">Файл `_Layout.cshtml` находится в папке `Pages/Shared` приложения Razor Pages или в папке `Views/Shared` приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="0da1e-171">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="0da1e-172">Добавьте тег `<script>` для скрипта `blazor.server.js` непосредственно перед разделом преобразования `Scripts`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-172">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="0da1e-173">Платформа добавляет скрипт `blazor.server.js` в приложение.</span><span class="sxs-lookup"><span data-stu-id="0da1e-173">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="0da1e-174">Добавлять скрипт в приложение вручную не требуется.</span><span class="sxs-lookup"><span data-stu-id="0da1e-174">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="0da1e-175">Добавьте файл `_Imports.razor` в корневую папку проекта со следующим содержимым (измените последнее пространство имен `MyAppNamespace` на пространство имен приложения):</span><span class="sxs-lookup"><span data-stu-id="0da1e-175">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="0da1e-176">Зарегистрируйте службу Blazor Server в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-176">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="0da1e-177">В `Startup.Configure` добавьте конечную точку Blazor Hub в `app.UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="0da1e-177">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="0da1e-178">Интегрируйте компоненты в какую-либо страницу или какое-либо представление.</span><span class="sxs-lookup"><span data-stu-id="0da1e-178">Integrate components into any page or view.</span></span> <span data-ttu-id="0da1e-179">Дополнительные сведения см. в разделе [Отрисовка компонентов со страницы или представления](#render-components-from-a-page-or-view).</span><span class="sxs-lookup"><span data-stu-id="0da1e-179">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="0da1e-180">Использование маршрутизируемых компонентов в приложении Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0da1e-180">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="0da1e-181">*Этот раздел описывает добавление компонентов, напрямую маршрутизируемых из запросов пользователей.*</span><span class="sxs-lookup"><span data-stu-id="0da1e-181">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="0da1e-182">Чтобы обеспечить поддержку маршрутизируемых компонентов Razor в приложениях Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="0da1e-182">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="0da1e-183">Следуйте указаниям в разделе [Конфигурация](#configuration).</span><span class="sxs-lookup"><span data-stu-id="0da1e-183">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="0da1e-184">Добавьте файл `App.razor` в корневой каталог проекта со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="0da1e-184">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="0da1e-185">Добавьте файл `_Host.cshtml` в папку `Pages` со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="0da1e-185">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="0da1e-186">Для макета компоненты используют общий файл `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="0da1e-186">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="0da1e-187">Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента `App`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="0da1e-188">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="0da1e-188">Is prerendered into the page.</span></span>
   * <span data-ttu-id="0da1e-189">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="0da1e-189">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="0da1e-190">Дополнительные сведения о вспомогательной функции тегов компонента, в том числе о передаче параметров и конфигурации <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, см. в статье <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="0da1e-190">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="0da1e-191">Добавьте маршрут с низким приоритетом для страницы `_Host.cshtml` в конфигурацию конечной точки в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-191">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="0da1e-192">Добавьте маршрутизируемые компоненты в приложение.</span><span class="sxs-lookup"><span data-stu-id="0da1e-192">Add routable components to the app.</span></span> <span data-ttu-id="0da1e-193">Пример:</span><span class="sxs-lookup"><span data-stu-id="0da1e-193">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="0da1e-194">Дополнительные сведения о пространствах имен см. в разделе [Пространства имен компонентов](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="0da1e-194">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="0da1e-195">Использование маршрутизируемых компонентов в приложении MVC</span><span class="sxs-lookup"><span data-stu-id="0da1e-195">Use routable components in an MVC app</span></span>

<span data-ttu-id="0da1e-196">*Этот раздел описывает добавление компонентов, напрямую маршрутизируемых из запросов пользователей.*</span><span class="sxs-lookup"><span data-stu-id="0da1e-196">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="0da1e-197">Для поддержки маршрутизируемых компонентов Razor в приложениях MVC сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="0da1e-197">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="0da1e-198">Следуйте указаниям в разделе [Конфигурация](#configuration).</span><span class="sxs-lookup"><span data-stu-id="0da1e-198">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="0da1e-199">Добавьте файл `App.razor` в корневой каталог проекта со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="0da1e-199">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="0da1e-200">Добавьте файл `_Host.cshtml` в папку `Views/Home` со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="0da1e-200">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="0da1e-201">Для макета компоненты используют общий файл `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="0da1e-201">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="0da1e-202">Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента `App`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="0da1e-203">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="0da1e-203">Is prerendered into the page.</span></span>
   * <span data-ttu-id="0da1e-204">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="0da1e-204">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="0da1e-205">Дополнительные сведения о вспомогательной функции тегов компонента, в том числе о передаче параметров и конфигурации <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, см. в статье <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="0da1e-205">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="0da1e-206">Добавьте действие в контроллер Home:</span><span class="sxs-lookup"><span data-stu-id="0da1e-206">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="0da1e-207">Добавьте маршрут с низким приоритетом для действия контроллера, которое возвращает представление `_Host.cshtml`, в конфигурацию конечной точки в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0da1e-207">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="0da1e-208">Создайте папку `Pages` и добавьте маршрутизируемые компоненты в приложение.</span><span class="sxs-lookup"><span data-stu-id="0da1e-208">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="0da1e-209">Пример:</span><span class="sxs-lookup"><span data-stu-id="0da1e-209">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="0da1e-210">Дополнительные сведения о пространствах имен см. в разделе [Пространства имен компонентов](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="0da1e-210">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="0da1e-211">Отрисовка компонентов со страницы или представления</span><span class="sxs-lookup"><span data-stu-id="0da1e-211">Render components from a page or view</span></span>

<span data-ttu-id="0da1e-212">*Этот раздел описывает добавление на страницы или в представления компонентов, не являющихся напрямую маршрутизируемыми из запросов пользователей.*</span><span class="sxs-lookup"><span data-stu-id="0da1e-212">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="0da1e-213">Чтобы отрисовать компонент из страницы или представления, используйте [вспомогательную функцию тега компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="0da1e-213">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="0da1e-214">Отрисовка интерактивных компонентов с отслеживанием состояния</span><span class="sxs-lookup"><span data-stu-id="0da1e-214">Render stateful interactive components</span></span>

<span data-ttu-id="0da1e-215">На страницу или в представление Razor можно добавить интерактивные компоненты с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="0da1e-215">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="0da1e-216">При отображении страницы или представления:</span><span class="sxs-lookup"><span data-stu-id="0da1e-216">When the page or view renders:</span></span>

* <span data-ttu-id="0da1e-217">компонент предварительно отображается страницей или представлением;</span><span class="sxs-lookup"><span data-stu-id="0da1e-217">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="0da1e-218">исходное состояние компонента, используемое для предварительной визуализации, теряется;</span><span class="sxs-lookup"><span data-stu-id="0da1e-218">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="0da1e-219">новое состояние компонента создается при установке подключения SignalR.</span><span class="sxs-lookup"><span data-stu-id="0da1e-219">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="0da1e-220">Следующая страница Razor визуализирует компонент `Counter`.</span><span class="sxs-lookup"><span data-stu-id="0da1e-220">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="0da1e-221">Дополнительные сведения см. в разделе <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="0da1e-221">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="0da1e-222">Отрисовка неинтерактивных компонентов</span><span class="sxs-lookup"><span data-stu-id="0da1e-222">Render noninteractive components</span></span>

<span data-ttu-id="0da1e-223">На следующей странице Razor компонент `Counter` статически подготавливается к просмотру с начальным значением, указанным с помощью формы.</span><span class="sxs-lookup"><span data-stu-id="0da1e-223">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="0da1e-224">Так как этот компонент отображается статически, он не может быть интерактивным:</span><span class="sxs-lookup"><span data-stu-id="0da1e-224">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="0da1e-225">Дополнительные сведения см. в разделе <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="0da1e-225">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="0da1e-226">Пространства имен компонентов</span><span class="sxs-lookup"><span data-stu-id="0da1e-226">Component namespaces</span></span>

<span data-ttu-id="0da1e-227">При использовании настраиваемой папки для хранения компонентов приложения добавьте пространство имен, представляющее эту папку, на страницу или в представление либо в файл `_ViewImports.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="0da1e-227">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="0da1e-228">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="0da1e-228">In the following example:</span></span>

* <span data-ttu-id="0da1e-229">Измените `MyAppNamespace` на пространство имен приложения.</span><span class="sxs-lookup"><span data-stu-id="0da1e-229">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="0da1e-230">Если папка с именем `Components` не используется для хранения компонентов, измените `Components` на папку, где находятся компоненты.</span><span class="sxs-lookup"><span data-stu-id="0da1e-230">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="0da1e-231">Файл `_ViewImports.cshtml` находится в папке `Pages` приложения Razor Pages или в папке `Views` приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="0da1e-231">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="0da1e-232">Дополнительные сведения см. в разделе <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="0da1e-232">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
