---
<span data-ttu-id="f5f21-101">title: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-101">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-103">'Blazor'</span></span>
- <span data-ttu-id="f5f21-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-104">'Identity'</span></span>
- <span data-ttu-id="f5f21-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-106">'Razor'</span></span>
- <span data-ttu-id="f5f21-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-107">'SignalR' uid:</span></span> 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="f5f21-108">Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f5f21-108">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="f5f21-109">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="f5f21-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="f5f21-110">Компоненты Razor можно интегрировать в приложения MVC и Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f5f21-110">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="f5f21-111">Одновременно с отрисовкой страницы или представления можно выполнять предварительную обработку компонентов.</span><span class="sxs-lookup"><span data-stu-id="f5f21-111">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="f5f21-112">После [подготовки приложения](#prepare-the-app) используйте рекомендации в следующих разделах в зависимости от требований приложения:</span><span class="sxs-lookup"><span data-stu-id="f5f21-112">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="f5f21-113">Маршрутизируемые компоненты. Для компонентов, напрямую маршрутизируемых из запросов пользователей.</span><span class="sxs-lookup"><span data-stu-id="f5f21-113">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="f5f21-114">Следуйте этому руководству, когда посетители должны иметь возможность сделать HTTP-запрос в браузере для компонента с директивой [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="f5f21-114">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="f5f21-115">[Использование маршрутизируемых компонентов в приложении Razor Pages](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="f5f21-115">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="f5f21-116">Использование маршрутизируемых компонентов в приложении MVC</span><span class="sxs-lookup"><span data-stu-id="f5f21-116">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="f5f21-117">[Отрисовка компонентов со страницы или представления.](#render-components-from-a-page-or-view) Для компонентов, которые не маршрутизируются напрямую из запросов пользователей.</span><span class="sxs-lookup"><span data-stu-id="f5f21-117">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="f5f21-118">Следуйте этому руководству, когда приложение внедряет компоненты в существующие страницы и представления с помощью вспомогательной функции [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f5f21-118">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="f5f21-119">Подготовка приложения</span><span class="sxs-lookup"><span data-stu-id="f5f21-119">Prepare the app</span></span>

<span data-ttu-id="f5f21-120">Существующее приложение MVC или Razor Pages может интегрировать компоненты Razor в страницы и представления:</span><span class="sxs-lookup"><span data-stu-id="f5f21-120">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="f5f21-121">В файле макета приложения ( *_Layout.cshtml*) сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="f5f21-121">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="f5f21-122">Добавьте следующий тег `<base>` в элемент `<head>`:</span><span class="sxs-lookup"><span data-stu-id="f5f21-122">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="f5f21-123">Значение `href` (*базовый путь к приложению* ) в предыдущем примере предполагает, что приложение находится по корневому URL-пути (`/`).</span><span class="sxs-lookup"><span data-stu-id="f5f21-123">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="f5f21-124">Если приложение является подчиненным, следуйте инструкциям в разделе *Базовый путь к приложению* статьи <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="f5f21-124">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="f5f21-125">Файл *_Layout.cshtml* находится в папке *Pages/Shared* приложения Razor Pages или папке *Views/Shared* приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="f5f21-125">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="f5f21-126">Добавьте тег `<script>` для скрипта *blazor.server.js* непосредственно перед закрывающим тегом `</body>`:</span><span class="sxs-lookup"><span data-stu-id="f5f21-126">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="f5f21-127">Платформа добавляет скрипт *blazor.server.js* в приложение.</span><span class="sxs-lookup"><span data-stu-id="f5f21-127">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="f5f21-128">Добавлять скрипт в приложение вручную не требуется.</span><span class="sxs-lookup"><span data-stu-id="f5f21-128">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="f5f21-129">Добавьте файл *_Imports.razor* в корневую папку проекта со следующим содержимым (измените последнее пространство имен `MyAppNamespace` на пространство имен приложения):</span><span class="sxs-lookup"><span data-stu-id="f5f21-129">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="f5f21-130">В `Startup.ConfigureServices` зарегистрируйте службу Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f5f21-130">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="f5f21-131">В `Startup.Configure` добавьте конечную точку Blazor Hub в `app.UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="f5f21-131">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="f5f21-132">Интегрируйте компоненты в какую-либо страницу или какое-либо представление.</span><span class="sxs-lookup"><span data-stu-id="f5f21-132">Integrate components into any page or view.</span></span> <span data-ttu-id="f5f21-133">Дополнительные сведения см. в разделе [Отрисовка компонентов со страницы или представления](#render-components-from-a-page-or-view).</span><span class="sxs-lookup"><span data-stu-id="f5f21-133">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="f5f21-134">Использование маршрутизируемых компонентов в приложении Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f5f21-134">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="f5f21-135">*Этот раздел описывает добавление компонентов, напрямую маршрутизируемых из запросов пользователей.*</span><span class="sxs-lookup"><span data-stu-id="f5f21-135">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="f5f21-136">Чтобы обеспечить поддержку маршрутизируемых компонентов Razor в приложениях Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="f5f21-136">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="f5f21-137">Следуйте указаниям в разделе [подготовка приложений](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="f5f21-137">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="f5f21-138">Добавьте файл *App.razor* в корневой каталог проекта со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="f5f21-138">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="f5f21-139">Добавьте файл *_Host.cshtml* в папку *Pages* со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="f5f21-139">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="f5f21-140">Для макета компоненты используют общий файл *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f5f21-140">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="f5f21-141">Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента `App`:</span><span class="sxs-lookup"><span data-stu-id="f5f21-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="f5f21-142">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="f5f21-142">Is prerendered into the page.</span></span>
   * <span data-ttu-id="f5f21-143">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="f5f21-143">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="f5f21-144">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="f5f21-144">Render Mode</span></span> | <span data-ttu-id="f5f21-145">Описание</span><span class="sxs-lookup"><span data-stu-id="f5f21-145">Description</span></span> |
   | ---
<span data-ttu-id="f5f21-146">title: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-146">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-148">'Blazor'</span></span>
- <span data-ttu-id="f5f21-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-149">'Identity'</span></span>
- <span data-ttu-id="f5f21-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-151">'Razor'</span></span>
- <span data-ttu-id="f5f21-152">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5f21-153">title: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-153">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-155">'Blazor'</span></span>
- <span data-ttu-id="f5f21-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-156">'Identity'</span></span>
- <span data-ttu-id="f5f21-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-158">'Razor'</span></span>
- <span data-ttu-id="f5f21-159">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5f21-160">title: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-160">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-162">'Blazor'</span></span>
- <span data-ttu-id="f5f21-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-163">'Identity'</span></span>
- <span data-ttu-id="f5f21-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-165">'Razor'</span></span>
- <span data-ttu-id="f5f21-166">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-166">'SignalR' uid:</span></span> 

<span data-ttu-id="f5f21-167">------ | --- название: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-167">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-169">'Blazor'</span></span>
- <span data-ttu-id="f5f21-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-170">'Identity'</span></span>
- <span data-ttu-id="f5f21-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-172">'Razor'</span></span>
- <span data-ttu-id="f5f21-173">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5f21-174">title: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-174">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-176">'Blazor'</span></span>
- <span data-ttu-id="f5f21-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-177">'Identity'</span></span>
- <span data-ttu-id="f5f21-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-179">'Razor'</span></span>
- <span data-ttu-id="f5f21-180">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5f21-181">title: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-181">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-183">'Blazor'</span></span>
- <span data-ttu-id="f5f21-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-184">'Identity'</span></span>
- <span data-ttu-id="f5f21-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-186">'Razor'</span></span>
- <span data-ttu-id="f5f21-187">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-187">'SignalR' uid:</span></span> 

<span data-ttu-id="f5f21-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Преобразует компонент `App` в статический HTML и включает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f5f21-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="f5f21-189">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="f5f21-189">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="f5f21-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Отображает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f5f21-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="f5f21-191">Выходные данные компонента `App` не включаются.</span><span class="sxs-lookup"><span data-stu-id="f5f21-191">Output from the `App` component isn't included.</span></span> <span data-ttu-id="f5f21-192">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="f5f21-192">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="f5f21-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Преобразует компонент `App` в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="f5f21-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="f5f21-194">Дополнительные сведения о компоненте Tag Helper см. в разделе <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="f5f21-194">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="f5f21-195">Добавьте маршрут с низким приоритетом для страницы *_Host.cshtml* в конфигурацию конечной точки в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f5f21-195">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="f5f21-196">Добавьте маршрутизируемые компоненты в приложение.</span><span class="sxs-lookup"><span data-stu-id="f5f21-196">Add routable components to the app.</span></span> <span data-ttu-id="f5f21-197">Пример:</span><span class="sxs-lookup"><span data-stu-id="f5f21-197">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="f5f21-198">Дополнительные сведения о пространствах имен см. в разделе [Пространства имен компонентов](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="f5f21-198">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="f5f21-199">Использование маршрутизируемых компонентов в приложении MVC</span><span class="sxs-lookup"><span data-stu-id="f5f21-199">Use routable components in an MVC app</span></span>

<span data-ttu-id="f5f21-200">*Этот раздел описывает добавление компонентов, напрямую маршрутизируемых из запросов пользователей.*</span><span class="sxs-lookup"><span data-stu-id="f5f21-200">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="f5f21-201">Для поддержки маршрутизируемых компонентов Razor в приложениях MVC сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="f5f21-201">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="f5f21-202">Следуйте указаниям в разделе [подготовка приложений](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="f5f21-202">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="f5f21-203">Добавьте файл *App.razor* в корневой каталог проекта со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="f5f21-203">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="f5f21-204">Добавьте файл *_Host.cshtml* в папку *Views/Home* со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="f5f21-204">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="f5f21-205">Для макета компоненты используют общий файл *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f5f21-205">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="f5f21-206">Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента `App`:</span><span class="sxs-lookup"><span data-stu-id="f5f21-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="f5f21-207">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="f5f21-207">Is prerendered into the page.</span></span>
   * <span data-ttu-id="f5f21-208">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="f5f21-208">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="f5f21-209">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="f5f21-209">Render Mode</span></span> | <span data-ttu-id="f5f21-210">Описание</span><span class="sxs-lookup"><span data-stu-id="f5f21-210">Description</span></span> |
   | ---
<span data-ttu-id="f5f21-211">title: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-211">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-213">'Blazor'</span></span>
- <span data-ttu-id="f5f21-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-214">'Identity'</span></span>
- <span data-ttu-id="f5f21-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-216">'Razor'</span></span>
- <span data-ttu-id="f5f21-217">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5f21-218">title: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-218">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-220">'Blazor'</span></span>
- <span data-ttu-id="f5f21-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-221">'Identity'</span></span>
- <span data-ttu-id="f5f21-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-223">'Razor'</span></span>
- <span data-ttu-id="f5f21-224">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5f21-225">title: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-225">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-227">'Blazor'</span></span>
- <span data-ttu-id="f5f21-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-228">'Identity'</span></span>
- <span data-ttu-id="f5f21-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-230">'Razor'</span></span>
- <span data-ttu-id="f5f21-231">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-231">'SignalR' uid:</span></span> 

<span data-ttu-id="f5f21-232">------ | --- название: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-232">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-234">'Blazor'</span></span>
- <span data-ttu-id="f5f21-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-235">'Identity'</span></span>
- <span data-ttu-id="f5f21-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-237">'Razor'</span></span>
- <span data-ttu-id="f5f21-238">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5f21-239">title: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-239">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-241">'Blazor'</span></span>
- <span data-ttu-id="f5f21-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-242">'Identity'</span></span>
- <span data-ttu-id="f5f21-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-244">'Razor'</span></span>
- <span data-ttu-id="f5f21-245">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5f21-246">title: 'Интеграция компонентов Razor ASP.NET Core в приложения MVC и Razor Pages' автор: описание: 'Сведения о сценариях привязки к данным в компонентах и элементах модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="f5f21-246">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f5f21-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5f21-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5f21-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-248">'Blazor'</span></span>
- <span data-ttu-id="f5f21-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5f21-249">'Identity'</span></span>
- <span data-ttu-id="f5f21-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5f21-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5f21-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5f21-251">'Razor'</span></span>
- <span data-ttu-id="f5f21-252">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f5f21-252">'SignalR' uid:</span></span> 

<span data-ttu-id="f5f21-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Преобразует компонент `App` в статический HTML и включает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f5f21-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="f5f21-254">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="f5f21-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="f5f21-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Отображает метку приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f5f21-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="f5f21-256">Выходные данные компонента `App` не включаются.</span><span class="sxs-lookup"><span data-stu-id="f5f21-256">Output from the `App` component isn't included.</span></span> <span data-ttu-id="f5f21-257">При запуске пользовательского агента эта метка используется для начальной загрузки приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="f5f21-257">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="f5f21-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Преобразует компонент `App` в статический HTML.</span><span class="sxs-lookup"><span data-stu-id="f5f21-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="f5f21-259">Дополнительные сведения о компоненте Tag Helper см. в разделе <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="f5f21-259">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="f5f21-260">Добавьте действие в контроллер Home:</span><span class="sxs-lookup"><span data-stu-id="f5f21-260">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="f5f21-261">Добавьте маршрут с низким приоритетом для действия контроллера, которое возвращает представление *_Host.cshtml*, в конфигурацию конечной точки в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f5f21-261">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="f5f21-262">Создайте папку *Pages* и добавьте маршрутизируемые компоненты в приложение.</span><span class="sxs-lookup"><span data-stu-id="f5f21-262">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="f5f21-263">Пример:</span><span class="sxs-lookup"><span data-stu-id="f5f21-263">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="f5f21-264">Дополнительные сведения о пространствах имен см. в разделе [Пространства имен компонентов](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="f5f21-264">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="f5f21-265">Отрисовка компонентов со страницы или представления</span><span class="sxs-lookup"><span data-stu-id="f5f21-265">Render components from a page or view</span></span>

<span data-ttu-id="f5f21-266">*Этот раздел описывает добавление на страницы или в представления компонентов, не являющихся напрямую маршрутизируемыми из запросов пользователей.*</span><span class="sxs-lookup"><span data-stu-id="f5f21-266">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="f5f21-267">Чтобы отрисовать компонент из страницы или представления, используйте [вспомогательную функцию тега компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f5f21-267">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="f5f21-268">Отрисовка интерактивных компонентов с отслеживанием состояния</span><span class="sxs-lookup"><span data-stu-id="f5f21-268">Render stateful interactive components</span></span>

<span data-ttu-id="f5f21-269">На страницу или в представление Razor можно добавить интерактивные компоненты с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="f5f21-269">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="f5f21-270">При отображении страницы или представления:</span><span class="sxs-lookup"><span data-stu-id="f5f21-270">When the page or view renders:</span></span>

* <span data-ttu-id="f5f21-271">компонент предварительно отображается страницей или представлением;</span><span class="sxs-lookup"><span data-stu-id="f5f21-271">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="f5f21-272">исходное состояние компонента, используемое для предварительной визуализации, теряется;</span><span class="sxs-lookup"><span data-stu-id="f5f21-272">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="f5f21-273">новое состояние компонента создается при установке подключения SignalR.</span><span class="sxs-lookup"><span data-stu-id="f5f21-273">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="f5f21-274">Следующая страница Razor визуализирует компонент `Counter`.</span><span class="sxs-lookup"><span data-stu-id="f5f21-274">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="f5f21-275">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="f5f21-275">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="f5f21-276">Отрисовка неинтерактивных компонентов</span><span class="sxs-lookup"><span data-stu-id="f5f21-276">Render noninteractive components</span></span>

<span data-ttu-id="f5f21-277">На следующей странице Razor компонент `Counter` статически подготавливается к просмотру с начальным значением, указанным с помощью формы.</span><span class="sxs-lookup"><span data-stu-id="f5f21-277">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="f5f21-278">Так как этот компонент отображается статически, он не может быть интерактивным:</span><span class="sxs-lookup"><span data-stu-id="f5f21-278">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="f5f21-279">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="f5f21-279">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="f5f21-280">Пространства имен компонентов</span><span class="sxs-lookup"><span data-stu-id="f5f21-280">Component namespaces</span></span>

<span data-ttu-id="f5f21-281">При использовании настраиваемой папки для хранения компонентов приложения добавьте пространство имен, представляющее эту папку, на страницу или в представление либо в файл *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f5f21-281">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="f5f21-282">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="f5f21-282">In the following example:</span></span>

* <span data-ttu-id="f5f21-283">Измените `MyAppNamespace` на пространство имен приложения.</span><span class="sxs-lookup"><span data-stu-id="f5f21-283">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="f5f21-284">Если папка с именем *Components* не используется для хранения компонентов, измените `Components` на папку, где находятся компоненты.</span><span class="sxs-lookup"><span data-stu-id="f5f21-284">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="f5f21-285">Файл *_ViewImports.cshtml* находится в папке *Pages* приложения Razor Pages или папке *Views* приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="f5f21-285">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="f5f21-286">Для получения дополнительной информации см. <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="f5f21-286">For more information, see <xref:blazor/components#import-components>.</span></span>
