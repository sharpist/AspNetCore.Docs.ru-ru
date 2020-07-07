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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: 29360174ea86623491d5d8aacd4038162855bba8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399066"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="460b6-103">Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="460b6-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="460b6-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="460b6-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="460b6-105">Компоненты Razor можно интегрировать в приложения MVC и Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="460b6-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="460b6-106">Одновременно с отрисовкой страницы или представления можно выполнять предварительную обработку компонентов.</span><span class="sxs-lookup"><span data-stu-id="460b6-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="460b6-107">После [подготовки приложения](#prepare-the-app) используйте рекомендации в следующих разделах в зависимости от требований приложения:</span><span class="sxs-lookup"><span data-stu-id="460b6-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="460b6-108">Маршрутизируемые компоненты. Для компонентов, напрямую маршрутизируемых из запросов пользователей.</span><span class="sxs-lookup"><span data-stu-id="460b6-108">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="460b6-109">Следуйте этому руководству, когда посетители должны иметь возможность сделать HTTP-запрос в браузере для компонента с директивой [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="460b6-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="460b6-110">[Использование маршрутизируемых компонентов в приложении Razor Pages](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="460b6-110">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="460b6-111">Использование маршрутизируемых компонентов в приложении MVC</span><span class="sxs-lookup"><span data-stu-id="460b6-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="460b6-112">[Отрисовка компонентов со страницы или представления.](#render-components-from-a-page-or-view) Для компонентов, которые не маршрутизируются напрямую из запросов пользователей.</span><span class="sxs-lookup"><span data-stu-id="460b6-112">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="460b6-113">Следуйте этому руководству, когда приложение внедряет компоненты в существующие страницы и представления с помощью вспомогательной функции [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="460b6-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="460b6-114">Подготовка приложения</span><span class="sxs-lookup"><span data-stu-id="460b6-114">Prepare the app</span></span>

<span data-ttu-id="460b6-115">Существующее приложение MVC или Razor Pages может интегрировать компоненты Razor в страницы и представления:</span><span class="sxs-lookup"><span data-stu-id="460b6-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="460b6-116">В файле макета приложения (`_Layout.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="460b6-116">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="460b6-117">Добавьте следующий тег `<base>` в элемент `<head>`:</span><span class="sxs-lookup"><span data-stu-id="460b6-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="460b6-118">Значение `href` (*базовый путь к приложению* ) в предыдущем примере предполагает, что приложение находится по корневому URL-пути (`/`).</span><span class="sxs-lookup"><span data-stu-id="460b6-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="460b6-119">Если приложение является подчиненным, следуйте инструкциям в разделе *Базовый путь к приложению* статьи <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="460b6-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="460b6-120">Файл `_Layout.cshtml` находится в папке *Pages/Shared* приложения Razor Pages или в папке *Views/Shared* приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="460b6-120">The `_Layout.cshtml` file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="460b6-121">Добавьте тег `<script>` для скрипта *blazor.server.js* непосредственно перед закрывающим тегом `</body>`:</span><span class="sxs-lookup"><span data-stu-id="460b6-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="460b6-122">Платформа добавляет скрипт *blazor.server.js* в приложение.</span><span class="sxs-lookup"><span data-stu-id="460b6-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="460b6-123">Добавлять скрипт в приложение вручную не требуется.</span><span class="sxs-lookup"><span data-stu-id="460b6-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="460b6-124">Добавьте файл `_Imports.razor` в корневую папку проекта со следующим содержимым (измените последнее пространство имен `MyAppNamespace` на пространство имен приложения):</span><span class="sxs-lookup"><span data-stu-id="460b6-124">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="460b6-125">Зарегистрируйте службу Blazor Server в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="460b6-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="460b6-126">В `Startup.Configure` добавьте конечную точку Blazor Hub в `app.UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="460b6-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="460b6-127">Интегрируйте компоненты в какую-либо страницу или какое-либо представление.</span><span class="sxs-lookup"><span data-stu-id="460b6-127">Integrate components into any page or view.</span></span> <span data-ttu-id="460b6-128">Дополнительные сведения см. в разделе [Отрисовка компонентов со страницы или представления](#render-components-from-a-page-or-view).</span><span class="sxs-lookup"><span data-stu-id="460b6-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="460b6-129">Использование маршрутизируемых компонентов в приложении Razor Pages</span><span class="sxs-lookup"><span data-stu-id="460b6-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="460b6-130">*Этот раздел описывает добавление компонентов, напрямую маршрутизируемых из запросов пользователей.*</span><span class="sxs-lookup"><span data-stu-id="460b6-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="460b6-131">Чтобы обеспечить поддержку маршрутизируемых компонентов Razor в приложениях Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="460b6-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="460b6-132">Следуйте указаниям в разделе [подготовка приложений](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="460b6-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="460b6-133">Добавьте файл `App.razor` в корневой каталог проекта со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="460b6-133">Add an `App.razor` file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="460b6-134">Добавьте файл `_Host.cshtml` в папку `Pages` со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="460b6-134">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="460b6-135">Для макета компоненты используют общий файл `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="460b6-135">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="460b6-136">Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента `App`:</span><span class="sxs-lookup"><span data-stu-id="460b6-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="460b6-137">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="460b6-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="460b6-138">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="460b6-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="460b6-139">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="460b6-139">Render Mode</span></span> | <span data-ttu-id="460b6-140">Описание</span><span class="sxs-lookup"><span data-stu-id="460b6-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="460b6-141">Преобразует компонент `App` в статический HTML и включает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="460b6-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="460b6-142">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="460b6-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="460b6-143">Отображает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="460b6-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="460b6-144">Выходные данные компонента `App` не включаются.</span><span class="sxs-lookup"><span data-stu-id="460b6-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="460b6-145">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="460b6-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="460b6-146">Преобразует компонент `App` в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="460b6-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="460b6-147">Дополнительные сведения о компоненте Tag Helper см. в разделе <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="460b6-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="460b6-148">Добавьте маршрут с низким приоритетом для страницы `_Host.cshtml` в конфигурацию конечной точки в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="460b6-148">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="460b6-149">Добавьте маршрутизируемые компоненты в приложение.</span><span class="sxs-lookup"><span data-stu-id="460b6-149">Add routable components to the app.</span></span> <span data-ttu-id="460b6-150">Пример:</span><span class="sxs-lookup"><span data-stu-id="460b6-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="460b6-151">Дополнительные сведения о пространствах имен см. в разделе [Пространства имен компонентов](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="460b6-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="460b6-152">Использование маршрутизируемых компонентов в приложении MVC</span><span class="sxs-lookup"><span data-stu-id="460b6-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="460b6-153">*Этот раздел описывает добавление компонентов, напрямую маршрутизируемых из запросов пользователей.*</span><span class="sxs-lookup"><span data-stu-id="460b6-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="460b6-154">Для поддержки маршрутизируемых компонентов Razor в приложениях MVC сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="460b6-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="460b6-155">Следуйте указаниям в разделе [подготовка приложений](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="460b6-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="460b6-156">Добавьте файл `App.razor` в корневой каталог проекта со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="460b6-156">Add an `App.razor` file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="460b6-157">Добавьте файл `_Host.cshtml` в папку `Views/Home` со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="460b6-157">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="460b6-158">Для макета компоненты используют общий файл `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="460b6-158">Components use the shared `_Layout.cshtml` file for their layout.</span></span>
   
   <span data-ttu-id="460b6-159">Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента `App`:</span><span class="sxs-lookup"><span data-stu-id="460b6-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="460b6-160">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="460b6-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="460b6-161">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="460b6-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="460b6-162">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="460b6-162">Render Mode</span></span> | <span data-ttu-id="460b6-163">Описание</span><span class="sxs-lookup"><span data-stu-id="460b6-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="460b6-164">Преобразует компонент `App` в статический HTML и включает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="460b6-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="460b6-165">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="460b6-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="460b6-166">Отображает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="460b6-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="460b6-167">Выходные данные компонента `App` не включаются.</span><span class="sxs-lookup"><span data-stu-id="460b6-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="460b6-168">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="460b6-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="460b6-169">Преобразует компонент `App` в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="460b6-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="460b6-170">Дополнительные сведения о компоненте Tag Helper см. в разделе <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="460b6-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="460b6-171">Добавьте действие в контроллер Home:</span><span class="sxs-lookup"><span data-stu-id="460b6-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="460b6-172">Добавьте маршрут с низким приоритетом для действия контроллера, которое возвращает представление `_Host.cshtml`, в конфигурацию конечной точки в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="460b6-172">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="460b6-173">Создайте папку `Pages` и добавьте маршрутизируемые компоненты в приложение.</span><span class="sxs-lookup"><span data-stu-id="460b6-173">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="460b6-174">Пример:</span><span class="sxs-lookup"><span data-stu-id="460b6-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="460b6-175">Дополнительные сведения о пространствах имен см. в разделе [Пространства имен компонентов](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="460b6-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="460b6-176">Отрисовка компонентов со страницы или представления</span><span class="sxs-lookup"><span data-stu-id="460b6-176">Render components from a page or view</span></span>

<span data-ttu-id="460b6-177">*Этот раздел описывает добавление на страницы или в представления компонентов, не являющихся напрямую маршрутизируемыми из запросов пользователей.*</span><span class="sxs-lookup"><span data-stu-id="460b6-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="460b6-178">Чтобы отрисовать компонент из страницы или представления, используйте [вспомогательную функцию тега компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="460b6-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="460b6-179">Отрисовка интерактивных компонентов с отслеживанием состояния</span><span class="sxs-lookup"><span data-stu-id="460b6-179">Render stateful interactive components</span></span>

<span data-ttu-id="460b6-180">На страницу или в представление Razor можно добавить интерактивные компоненты с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="460b6-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="460b6-181">При отображении страницы или представления:</span><span class="sxs-lookup"><span data-stu-id="460b6-181">When the page or view renders:</span></span>

* <span data-ttu-id="460b6-182">компонент предварительно отображается страницей или представлением;</span><span class="sxs-lookup"><span data-stu-id="460b6-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="460b6-183">исходное состояние компонента, используемое для предварительной визуализации, теряется;</span><span class="sxs-lookup"><span data-stu-id="460b6-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="460b6-184">новое состояние компонента создается при установке подключения SignalR.</span><span class="sxs-lookup"><span data-stu-id="460b6-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="460b6-185">Следующая страница Razor визуализирует компонент `Counter`.</span><span class="sxs-lookup"><span data-stu-id="460b6-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="460b6-186">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="460b6-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="460b6-187">Отрисовка неинтерактивных компонентов</span><span class="sxs-lookup"><span data-stu-id="460b6-187">Render noninteractive components</span></span>

<span data-ttu-id="460b6-188">На следующей странице Razor компонент `Counter` статически подготавливается к просмотру с начальным значением, указанным с помощью формы.</span><span class="sxs-lookup"><span data-stu-id="460b6-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="460b6-189">Так как этот компонент отображается статически, он не может быть интерактивным:</span><span class="sxs-lookup"><span data-stu-id="460b6-189">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="460b6-190">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="460b6-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="460b6-191">Пространства имен компонентов</span><span class="sxs-lookup"><span data-stu-id="460b6-191">Component namespaces</span></span>

<span data-ttu-id="460b6-192">При использовании настраиваемой папки для хранения компонентов приложения добавьте пространство имен, представляющее эту папку, на страницу или в представление либо в файл `_ViewImports.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="460b6-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="460b6-193">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="460b6-193">In the following example:</span></span>

* <span data-ttu-id="460b6-194">Измените `MyAppNamespace` на пространство имен приложения.</span><span class="sxs-lookup"><span data-stu-id="460b6-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="460b6-195">Если папка с именем *Components* не используется для хранения компонентов, измените `Components` на папку, где находятся компоненты.</span><span class="sxs-lookup"><span data-stu-id="460b6-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="460b6-196">Файл `_ViewImports.cshtml` находится в папке `Pages` приложения Razor Pages или в папке `Views` приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="460b6-196">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="460b6-197">Для получения дополнительной информации см. <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="460b6-197">For more information, see <xref:blazor/components/index#namespaces>.</span></span>
