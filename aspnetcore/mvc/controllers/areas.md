---
title: Области в ASP.NET Core
author: rick-anderson
description: Сведения о том, что области — это возможность ASP.NET MVC, которая служит для объединения связанных функций в группу в виде отдельного пространства имен (для маршрутизации) и структуры папок (для представлений).
ms.author: riande
ms.date: 03/21/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/controllers/areas
ms.openlocfilehash: 42eec406813adce4d7edbc1ab66a1f689c4aca0e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053531"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="2cdc1-103">Области в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2cdc1-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="2cdc1-104">[Дхананжай Кумар](https://twitter.com/debug_mode) и [Рик Андерсон (](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2cdc1-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2cdc1-105">Области — это ASP.NET функция, используемая для организации связанных функций в группе в виде отдельных:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="2cdc1-106">Пространство имен для маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-106">Namespace for routing.</span></span>
* <span data-ttu-id="2cdc1-107">Структура папок для представлений и Razor страниц.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="2cdc1-108">При использовании областей создается иерархия для маршрутизации путем добавления другого параметра маршрута, `area` , в и на `controller` `action` Razor страницу `page` .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="2cdc1-109">Области позволяют секционировать веб-приложение ASP.NET Core в более мелкие функциональные группы, каждый из которых имеет собственный набор Razor страниц, контроллеров, представлений и моделей.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="2cdc1-110">По сути, область является структурой внутри приложения.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="2cdc1-111">В веб-проекте ASP.NET Core логические компоненты, например страницы, модель, контроллер и представление, хранятся в разных папках.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="2cdc1-112">Среда выполнения ASP.NET Core использует соглашения об именовании для создания связи между этими компонентами.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="2cdc1-113">Крупное приложение может быть целесообразно разделить на отдельные высокоуровневые области функциональности.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="2cdc1-114">Например, в приложении для электронной коммерции можно выделить несколько подразделений: для оформления заказов, выставления счетов или поиска.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="2cdc1-115">Каждая из этих единиц имеет собственную область для хранения представлений, контроллеров, Razor страниц и моделей.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="2cdc1-116">Использовать области в проекте рекомендуется в таких случаях:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="2cdc1-117">Приложение состоит из нескольких высокоуровневых функциональных частей, которые могут быть разделены логически.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="2cdc1-118">Необходимо разделить приложение так, чтобы с каждой функциональной областью можно было работать отдельно.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="2cdc1-119">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="2cdc1-120">Пример загрузки содержит простое приложение для тестирования областей.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="2cdc1-121">Если вы используете Razor страницы, см. раздел [области со Razor страницами](#areas-with-razor-pages) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="2cdc1-122">Области для контроллеров с представлениями</span><span class="sxs-lookup"><span data-stu-id="2cdc1-122">Areas for controllers with views</span></span>

<span data-ttu-id="2cdc1-123">Типичное веб-приложение ASP.NET Core, использующее области, контроллеры и представления, содержит следующие элементы.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="2cdc1-124">[Структура папки области](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="2cdc1-125">Контроллеры с [`[Area]`](#attribute) атрибутом, который связывает контроллер с областью:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="2cdc1-126">[Маршрут к области, добавленный к запуску](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="2cdc1-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="2cdc1-127">Структура папки области</span><span class="sxs-lookup"><span data-stu-id="2cdc1-127">Area folder structure</span></span>

<span data-ttu-id="2cdc1-128">Рассмотрим приложение с двумя логическими группами: *товары* и *услуги* .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-128">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="2cdc1-129">При использовании областей структура папок будет выглядеть следующим образом.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="2cdc1-130">Имя проекта</span><span class="sxs-lookup"><span data-stu-id="2cdc1-130">Project name</span></span>
  * <span data-ttu-id="2cdc1-131">Области</span><span class="sxs-lookup"><span data-stu-id="2cdc1-131">Areas</span></span>
    * <span data-ttu-id="2cdc1-132">Продукты</span><span class="sxs-lookup"><span data-stu-id="2cdc1-132">Products</span></span>
      * <span data-ttu-id="2cdc1-133">Контроллеры</span><span class="sxs-lookup"><span data-stu-id="2cdc1-133">Controllers</span></span>
        * <span data-ttu-id="2cdc1-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="2cdc1-134">HomeController.cs</span></span>
        * <span data-ttu-id="2cdc1-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="2cdc1-135">ManageController.cs</span></span>
      * <span data-ttu-id="2cdc1-136">Представления</span><span class="sxs-lookup"><span data-stu-id="2cdc1-136">Views</span></span>
        * <span data-ttu-id="2cdc1-137">Домашняя страница</span><span class="sxs-lookup"><span data-stu-id="2cdc1-137">Home</span></span>
          * <span data-ttu-id="2cdc1-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2cdc1-138">Index.cshtml</span></span>
        * <span data-ttu-id="2cdc1-139">Управление</span><span class="sxs-lookup"><span data-stu-id="2cdc1-139">Manage</span></span>
          * <span data-ttu-id="2cdc1-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2cdc1-140">Index.cshtml</span></span>
          * <span data-ttu-id="2cdc1-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="2cdc1-141">About.cshtml</span></span>
    * <span data-ttu-id="2cdc1-142">Службы</span><span class="sxs-lookup"><span data-stu-id="2cdc1-142">Services</span></span>
      * <span data-ttu-id="2cdc1-143">Контроллеры</span><span class="sxs-lookup"><span data-stu-id="2cdc1-143">Controllers</span></span>
        * <span data-ttu-id="2cdc1-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="2cdc1-144">HomeController.cs</span></span>
      * <span data-ttu-id="2cdc1-145">Представления</span><span class="sxs-lookup"><span data-stu-id="2cdc1-145">Views</span></span>
        * <span data-ttu-id="2cdc1-146">Домашняя страница</span><span class="sxs-lookup"><span data-stu-id="2cdc1-146">Home</span></span>
          * <span data-ttu-id="2cdc1-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2cdc1-147">Index.cshtml</span></span>

<span data-ttu-id="2cdc1-148">Хотя предыдущий макет часто используется при работе с областями, только файлы представления необходимы для использования этой структуры папок.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="2cdc1-149">Обнаружение подходящего файла представления области происходит в следующем порядке.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="2cdc1-150">Привязка контроллера к области</span><span class="sxs-lookup"><span data-stu-id="2cdc1-150">Associate the controller with an Area</span></span>

<span data-ttu-id="2cdc1-151">Контроллеры областей обозначены атрибутом [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="2cdc1-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="2cdc1-152">Добавление маршрута области</span><span class="sxs-lookup"><span data-stu-id="2cdc1-152">Add Area route</span></span>

<span data-ttu-id="2cdc1-153">Маршруты с областями обычно используют  [обычную маршрутизацию](xref:mvc/controllers/routing#cr) , а не [маршрутизацию атрибутов](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="2cdc1-154">При маршрутизации на основе соглашений учитывается порядок.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2cdc1-155">Как правило, маршруты с областями должны находиться раньше в таблице маршрутов, так как они более точные, чем маршруты без областей.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="2cdc1-156">`{area:...}` можно использовать как токен в шаблонах маршрутов, если пространство URL-адресов одинаково во всех областях:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="2cdc1-157">В приведенном выше коде `exists` применяет ограничение, связанное с тем, что маршрут должен соответствовать области.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="2cdc1-158">Использование `{area:...}` с `MapControllerRoute` :</span><span class="sxs-lookup"><span data-stu-id="2cdc1-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="2cdc1-159">— Это наименее сложный механизм для добавления маршрутизации в области.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="2cdc1-160">Соответствует всем контроллерам с `[Area("Area name")]` атрибутом.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="2cdc1-161">В следующем коде используется <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> для создания двух именованных маршрутов областей:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="2cdc1-162">Дополнительные сведения см. в разделе [Маршрутизация области](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="2cdc1-163">Создание ссылки с областями MVC</span><span class="sxs-lookup"><span data-stu-id="2cdc1-163">Link generation with MVC areas</span></span>

<span data-ttu-id="2cdc1-164">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) показано создание ссылок с указанной областью:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="2cdc1-165">Пример загрузки включает [частичное представление](xref:mvc/views/partial) , содержащее:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="2cdc1-166">Предыдущие ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-166">The preceding links.</span></span>
* <span data-ttu-id="2cdc1-167">Ссылки, аналогичные предыдущим, за исключением, `area` не указаны.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="2cdc1-168">Частичное представление указывается в [файле макета](xref:mvc/views/layout), поэтому каждая страница в приложении отображает созданные ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="2cdc1-169">Ссылки, создаваемые без указания области, допустимы только при обращении со страницы в одной области и контроллере.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="2cdc1-170">Если область или контроллер не указаны, маршрутизация зависит от значений [окружения](xref:mvc/controllers/routing#ambient).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="2cdc1-171">Текущие значения маршрута текущего запроса считаются значениями окружения для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2cdc1-172">Во многих случаях для примера приложения использование внешних значений создает неверные ссылки с разметкой, которая не указывает область.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="2cdc1-173">Дополнительные сведения см. в разделе [Маршрутизация к действиям контроллера](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="2cdc1-174">Общий макет для областей с использованием файла _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="2cdc1-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="2cdc1-175">Чтобы поделиться общим макетом для всего приложения, в [корневой папке приложения](#arf)следует использовать файл *_ViewStart. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="2cdc1-176">Дополнительные сведения см. в разделе <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="2cdc1-177">Корневая папка приложения</span><span class="sxs-lookup"><span data-stu-id="2cdc1-177">Application root folder</span></span>

<span data-ttu-id="2cdc1-178">Корневая папка приложения — это папка, содержащая *Startup.CS* в веб-приложении, созданном с помощью шаблонов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="2cdc1-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="2cdc1-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="2cdc1-180">*/Виевс/_ViewImports. cshtml* , for MVC и */Пажес/_ViewImports. cshtml* для Razor страниц, не импортируется в представления в областях.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-180">*/Views/_ViewImports.cshtml* , for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="2cdc1-181">Используйте один из следующих подходов, чтобы предоставить импорты представлений для всех представлений:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="2cdc1-182">Добавьте *_ViewImports. cshtml* в [корневую папку приложения](#arf).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="2cdc1-183">*_ViewImports. cshtml* в корневой папке приложения будет применяться ко всем представлениям в приложении.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="2cdc1-184">Скопируйте файл *_ViewImports. cshtml* в соответствующую папку представления в разделе области.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="2cdc1-185">Файл *_ViewImports. cshtml* обычно содержит [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) Imports, `@using` и `@inject` .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="2cdc1-186">Дополнительные сведения см. в разделе [Импорт общих директив](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="2cdc1-187">Измените папку области по умолчанию, где хранятся представления</span><span class="sxs-lookup"><span data-stu-id="2cdc1-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="2cdc1-188">Следующий код изменяет папку области по умолчанию с `"Areas"` на `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="2cdc1-189">Области со Razor страницами</span><span class="sxs-lookup"><span data-stu-id="2cdc1-189">Areas with Razor Pages</span></span>

<span data-ttu-id="2cdc1-190">Для областей со Razor страницами требуется `Areas/<area name>/Pages` Папка в корне приложения.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="2cdc1-191">В [этом примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) используется следующая структура папок:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="2cdc1-192">Имя проекта</span><span class="sxs-lookup"><span data-stu-id="2cdc1-192">Project name</span></span>
  * <span data-ttu-id="2cdc1-193">Области</span><span class="sxs-lookup"><span data-stu-id="2cdc1-193">Areas</span></span>
    * <span data-ttu-id="2cdc1-194">Продукты</span><span class="sxs-lookup"><span data-stu-id="2cdc1-194">Products</span></span>
      * <span data-ttu-id="2cdc1-195">Страницы</span><span class="sxs-lookup"><span data-stu-id="2cdc1-195">Pages</span></span>
        * <span data-ttu-id="2cdc1-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="2cdc1-196">_ViewImports</span></span>
        * <span data-ttu-id="2cdc1-197">Сведения</span><span class="sxs-lookup"><span data-stu-id="2cdc1-197">About</span></span>
        * <span data-ttu-id="2cdc1-198">Индекс</span><span class="sxs-lookup"><span data-stu-id="2cdc1-198">Index</span></span>
    * <span data-ttu-id="2cdc1-199">Службы</span><span class="sxs-lookup"><span data-stu-id="2cdc1-199">Services</span></span>
      * <span data-ttu-id="2cdc1-200">Страницы</span><span class="sxs-lookup"><span data-stu-id="2cdc1-200">Pages</span></span>
        * <span data-ttu-id="2cdc1-201">Управление</span><span class="sxs-lookup"><span data-stu-id="2cdc1-201">Manage</span></span>
          * <span data-ttu-id="2cdc1-202">Сведения</span><span class="sxs-lookup"><span data-stu-id="2cdc1-202">About</span></span>
          * <span data-ttu-id="2cdc1-203">Индекс</span><span class="sxs-lookup"><span data-stu-id="2cdc1-203">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="2cdc1-204">Создание ссылок с помощью Razor страниц и областей</span><span class="sxs-lookup"><span data-stu-id="2cdc1-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="2cdc1-205">В следующем коде из [этого примера](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) показано создание ссылок с указанной областью (например, `asp-area="Products"`).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="2cdc1-206">Пример загрузки включает [частичное представление](xref:mvc/views/partial), которое содержит указанные выше ссылки и те же ссылки без указания области.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="2cdc1-207">Частичное представление указывается в [файле макета](xref:mvc/views/layout), поэтому каждая страница в приложении отображает созданные ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="2cdc1-208">Ссылки, создаваемые без указания области, допустимы только при обращении со страницы в одной области.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="2cdc1-209">Если область не указана, маршрутизация зависит от значений *окружения* .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="2cdc1-210">Текущие значения маршрута текущего запроса считаются значениями окружения для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2cdc1-211">Во многих случаях для примера приложения при использовании значений окружения создаются неверные ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="2cdc1-212">Для примера рассмотрим ссылки, созданные с помощью следующего кода.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="2cdc1-213">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-213">For the preceding code:</span></span>

* <span data-ttu-id="2cdc1-214">Ссылка, созданная с помощью `<a asp-page="/Manage/About">` будет правильной, только если последний запрос был к странице в области `Services`.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="2cdc1-215">Например, `/Services/Manage/`, `/Services/Manage/Index` или `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="2cdc1-216">Ссылка, созданная с помощью `<a asp-page="/About">` будет правильной, только если последний запрос был к странице в области `/Home`.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="2cdc1-217">Это код из [этого примера](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="2cdc1-218">Импорт пространства имен и вспомогательных функций тегов с помощью файла _ViewImports</span><span class="sxs-lookup"><span data-stu-id="2cdc1-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="2cdc1-219">Файл *_ViewImports. cshtml* можно добавить в каждую папку *страниц* с областями для импорта пространства имен и вспомогательных функций тегов на каждую Razor страницу в папке.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="2cdc1-220">Рассмотрите область *служб* в примере кода, которая не содержит файл *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="2cdc1-221">Следующая разметка показывает страницу */сервицес/манаже/абаут* Razor :</span><span class="sxs-lookup"><span data-stu-id="2cdc1-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="2cdc1-222">В этой разметке:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-222">In the preceding markup:</span></span>

* <span data-ttu-id="2cdc1-223">Для указания модели (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`) необходимо использовать полное доменное имя.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="2cdc1-224">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) включены `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="2cdc1-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="2cdc1-225">В примере загрузка область продуктов содержит следующий файл *_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2cdc1-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="2cdc1-226">Следующая разметка показывает страницу */Продуктс/абаут* Razor :</span><span class="sxs-lookup"><span data-stu-id="2cdc1-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="2cdc1-227">В предыдущем файле пространство имен и директива `@addTagHelper` импортируются в файл с помощью файла *Areas/Products/Pages/_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="2cdc1-228">Дополнительные сведения см. в разделах [Управление областью действия вспомогательной функции тега](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) и [Импорт общих директив](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="2cdc1-229">Общий макет для Razor областей страниц</span><span class="sxs-lookup"><span data-stu-id="2cdc1-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="2cdc1-230">Чтобы совместно использовать общий макет для всего приложения, переместите *_ViewStart.cshtml* в корневую папку приложения.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="2cdc1-231">Публикация областей</span><span class="sxs-lookup"><span data-stu-id="2cdc1-231">Publishing Areas</span></span>

<span data-ttu-id="2cdc1-232">Все файлы CSHTML и файлы в каталоге *wwwroot* публикуются в выходных данных, если в файл CSPROJ включен `<Project Sdk="Microsoft.NET.Sdk.Web">`.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2cdc1-233">Области — это возможность ASP.NET, которая служит для объединения связанных функций в группу в виде отдельного пространства имен (для маршрутизации) и структуры папок (для представлений).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="2cdc1-234">При использовании областей создается иерархия для маршрутизации путем добавления другого параметра маршрута, `area` , в и на `controller` `action` Razor страницу `page` .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="2cdc1-235">Области позволяют секционировать веб-приложение ASP.NET Core в более мелкие функциональные группы, каждый из которых имеет собственный набор Razor страниц, контроллеров, представлений и моделей.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="2cdc1-236">По сути, область является структурой внутри приложения.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="2cdc1-237">В веб-проекте ASP.NET Core логические компоненты, например страницы, модель, контроллер и представление, хранятся в разных папках.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="2cdc1-238">Среда выполнения ASP.NET Core использует соглашения об именовании для создания связи между этими компонентами.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="2cdc1-239">Крупное приложение может быть целесообразно разделить на отдельные высокоуровневые области функциональности.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="2cdc1-240">Например, в приложении для электронной коммерции можно выделить несколько подразделений: для оформления заказов, выставления счетов или поиска.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="2cdc1-241">Каждая из этих единиц имеет собственную область для хранения представлений, контроллеров, Razor страниц и моделей.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="2cdc1-242">Использовать области в проекте рекомендуется в таких случаях:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="2cdc1-243">Приложение состоит из нескольких высокоуровневых функциональных частей, которые могут быть разделены логически.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="2cdc1-244">Необходимо разделить приложение так, чтобы с каждой функциональной областью можно было работать отдельно.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="2cdc1-245">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="2cdc1-246">Пример загрузки содержит простое приложение для тестирования областей.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="2cdc1-247">Если вы используете Razor страницы, см. раздел [области со Razor страницами](#areas-with-razor-pages) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="2cdc1-248">Области для контроллеров с представлениями</span><span class="sxs-lookup"><span data-stu-id="2cdc1-248">Areas for controllers with views</span></span>

<span data-ttu-id="2cdc1-249">Типичное веб-приложение ASP.NET Core, использующее области, контроллеры и представления, содержит следующие элементы.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="2cdc1-250">[Структура папки области](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="2cdc1-251">Контроллеры с [`[Area]`](#attribute) атрибутом, который связывает контроллер с областью:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="2cdc1-252">[Маршрут к области, добавленный к запуску](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="2cdc1-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="2cdc1-253">Структура папки области</span><span class="sxs-lookup"><span data-stu-id="2cdc1-253">Area folder structure</span></span>

<span data-ttu-id="2cdc1-254">Рассмотрим приложение с двумя логическими группами: *товары* и *услуги* .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-254">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="2cdc1-255">При использовании областей структура папок будет выглядеть следующим образом.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="2cdc1-256">Имя проекта</span><span class="sxs-lookup"><span data-stu-id="2cdc1-256">Project name</span></span>
  * <span data-ttu-id="2cdc1-257">Области</span><span class="sxs-lookup"><span data-stu-id="2cdc1-257">Areas</span></span>
    * <span data-ttu-id="2cdc1-258">Продукты</span><span class="sxs-lookup"><span data-stu-id="2cdc1-258">Products</span></span>
      * <span data-ttu-id="2cdc1-259">Контроллеры</span><span class="sxs-lookup"><span data-stu-id="2cdc1-259">Controllers</span></span>
        * <span data-ttu-id="2cdc1-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="2cdc1-260">HomeController.cs</span></span>
        * <span data-ttu-id="2cdc1-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="2cdc1-261">ManageController.cs</span></span>
      * <span data-ttu-id="2cdc1-262">Представления</span><span class="sxs-lookup"><span data-stu-id="2cdc1-262">Views</span></span>
        * <span data-ttu-id="2cdc1-263">Домашняя страница</span><span class="sxs-lookup"><span data-stu-id="2cdc1-263">Home</span></span>
          * <span data-ttu-id="2cdc1-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2cdc1-264">Index.cshtml</span></span>
        * <span data-ttu-id="2cdc1-265">Управление</span><span class="sxs-lookup"><span data-stu-id="2cdc1-265">Manage</span></span>
          * <span data-ttu-id="2cdc1-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2cdc1-266">Index.cshtml</span></span>
          * <span data-ttu-id="2cdc1-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="2cdc1-267">About.cshtml</span></span>
    * <span data-ttu-id="2cdc1-268">Службы</span><span class="sxs-lookup"><span data-stu-id="2cdc1-268">Services</span></span>
      * <span data-ttu-id="2cdc1-269">Контроллеры</span><span class="sxs-lookup"><span data-stu-id="2cdc1-269">Controllers</span></span>
        * <span data-ttu-id="2cdc1-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="2cdc1-270">HomeController.cs</span></span>
      * <span data-ttu-id="2cdc1-271">Представления</span><span class="sxs-lookup"><span data-stu-id="2cdc1-271">Views</span></span>
        * <span data-ttu-id="2cdc1-272">Домашняя страница</span><span class="sxs-lookup"><span data-stu-id="2cdc1-272">Home</span></span>
          * <span data-ttu-id="2cdc1-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2cdc1-273">Index.cshtml</span></span>

<span data-ttu-id="2cdc1-274">Хотя предыдущий макет часто используется при работе с областями, только файлы представления необходимы для использования этой структуры папок.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="2cdc1-275">Обнаружение подходящего файла представления области происходит в следующем порядке.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="2cdc1-276">Привязка контроллера к области</span><span class="sxs-lookup"><span data-stu-id="2cdc1-276">Associate the controller with an Area</span></span>

<span data-ttu-id="2cdc1-277">Контроллеры областей обозначены атрибутом [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="2cdc1-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="2cdc1-278">Добавление маршрута области</span><span class="sxs-lookup"><span data-stu-id="2cdc1-278">Add Area route</span></span>

<span data-ttu-id="2cdc1-279">Маршруты области обычно используют маршрутизацию на основе соглашений, а не атрибутов.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="2cdc1-280">При маршрутизации на основе соглашений учитывается порядок.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2cdc1-281">Как правило, маршруты с областями должны находиться раньше в таблице маршрутов, так как они более точные, чем маршруты без областей.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="2cdc1-282">`{area:...}` можно использовать как токен в шаблонах маршрутов, если пространство URL-адресов одинаково во всех областях:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="2cdc1-283">В приведенном выше коде `exists` применяет ограничение, связанное с тем, что маршрут должен соответствовать области.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="2cdc1-284">Использование `{area:...}` — это наименее сложный механизм для добавления маршрута к областям.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="2cdc1-285">В следующем коде используется <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> для создания двух именованных маршрутов областей:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="2cdc1-286">При использовании `MapAreaRoute` с ASP.NET Core 2.2 см. [эту задачу GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="2cdc1-287">Дополнительные сведения см. в разделе [Маршрутизация области](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="2cdc1-288">Создание ссылки с областями MVC</span><span class="sxs-lookup"><span data-stu-id="2cdc1-288">Link generation with MVC areas</span></span>

<span data-ttu-id="2cdc1-289">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) показано создание ссылок с указанной областью:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="2cdc1-290">Ссылки, создаваемые предыдущим кодом, действуют в любом месте приложения.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="2cdc1-291">Пример загрузки включает [частичное представление](xref:mvc/views/partial), которое содержит указанные выше ссылки и те же ссылки без указания области.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="2cdc1-292">Частичное представление указывается в [файле макета](xref:mvc/views/layout), поэтому каждая страница в приложении отображает созданные ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="2cdc1-293">Ссылки, создаваемые без указания области, допустимы только при обращении со страницы в одной области и контроллере.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="2cdc1-294">Если область или контроллер не указаны, маршрутизация зависит от значений *окружения* .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="2cdc1-295">Текущие значения маршрута текущего запроса считаются значениями окружения для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2cdc1-296">Во многих случаях для примера приложения при использовании значений окружения создаются неверные ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="2cdc1-297">Дополнительные сведения см. в разделе [Маршрутизация к действиям контроллера](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="2cdc1-298">Общий макет для областей с использованием файла _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="2cdc1-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="2cdc1-299">Чтобы совместно использовать общий макет для всего приложения, переместите *_ViewStart.cshtml* в корневую папку приложения.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="2cdc1-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="2cdc1-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="2cdc1-301">В стандартном расположении файл */Views/_ViewImports.cshtml* не применяется к областям.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="2cdc1-302">Чтобы использовать общие [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro), `@using` или `@inject` в вашем регионе, убедитесь, что к [вашим представлениям в области применяется](xref:mvc/views/layout#importing-shared-directives)правильный файл *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="2cdc1-303">Чтобы поведение во всех представлениях было одинаковым, переместите файл */Views/_ViewImports.cshtml* в корневую папку приложения.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="2cdc1-304">Измените папку области по умолчанию, где хранятся представления</span><span class="sxs-lookup"><span data-stu-id="2cdc1-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="2cdc1-305">Следующий код изменяет папку области по умолчанию с `"Areas"` на `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="2cdc1-306">Области со Razor страницами</span><span class="sxs-lookup"><span data-stu-id="2cdc1-306">Areas with Razor Pages</span></span>

<span data-ttu-id="2cdc1-307">Для областей со Razor страницами требуется `Areas/<area name>/Pages` Папка в корне приложения.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="2cdc1-308">В [этом примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) используется следующая структура папок:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="2cdc1-309">Имя проекта</span><span class="sxs-lookup"><span data-stu-id="2cdc1-309">Project name</span></span>
  * <span data-ttu-id="2cdc1-310">Области</span><span class="sxs-lookup"><span data-stu-id="2cdc1-310">Areas</span></span>
    * <span data-ttu-id="2cdc1-311">Продукты</span><span class="sxs-lookup"><span data-stu-id="2cdc1-311">Products</span></span>
      * <span data-ttu-id="2cdc1-312">Страницы</span><span class="sxs-lookup"><span data-stu-id="2cdc1-312">Pages</span></span>
        * <span data-ttu-id="2cdc1-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="2cdc1-313">_ViewImports</span></span>
        * <span data-ttu-id="2cdc1-314">Сведения</span><span class="sxs-lookup"><span data-stu-id="2cdc1-314">About</span></span>
        * <span data-ttu-id="2cdc1-315">Индекс</span><span class="sxs-lookup"><span data-stu-id="2cdc1-315">Index</span></span>
    * <span data-ttu-id="2cdc1-316">Службы</span><span class="sxs-lookup"><span data-stu-id="2cdc1-316">Services</span></span>
      * <span data-ttu-id="2cdc1-317">Страницы</span><span class="sxs-lookup"><span data-stu-id="2cdc1-317">Pages</span></span>
        * <span data-ttu-id="2cdc1-318">Управление</span><span class="sxs-lookup"><span data-stu-id="2cdc1-318">Manage</span></span>
          * <span data-ttu-id="2cdc1-319">Сведения</span><span class="sxs-lookup"><span data-stu-id="2cdc1-319">About</span></span>
          * <span data-ttu-id="2cdc1-320">Индекс</span><span class="sxs-lookup"><span data-stu-id="2cdc1-320">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="2cdc1-321">Создание ссылок с помощью Razor страниц и областей</span><span class="sxs-lookup"><span data-stu-id="2cdc1-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="2cdc1-322">В следующем коде из [этого примера](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) показано создание ссылок с указанной областью (например, `asp-area="Products"`).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="2cdc1-323">Ссылки, создаваемые предыдущим кодом, действуют в любом месте приложения.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="2cdc1-324">Пример загрузки включает [частичное представление](xref:mvc/views/partial), которое содержит указанные выше ссылки и те же ссылки без указания области.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="2cdc1-325">Частичное представление указывается в [файле макета](xref:mvc/views/layout), поэтому каждая страница в приложении отображает созданные ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="2cdc1-326">Ссылки, создаваемые без указания области, допустимы только при обращении со страницы в одной области.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="2cdc1-327">Если область не указана, маршрутизация зависит от значений *окружения* .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="2cdc1-328">Текущие значения маршрута текущего запроса считаются значениями окружения для создания ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2cdc1-329">Во многих случаях для примера приложения при использовании значений окружения создаются неверные ссылки.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="2cdc1-330">Для примера рассмотрим ссылки, созданные с помощью следующего кода.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="2cdc1-331">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-331">For the preceding code:</span></span>

* <span data-ttu-id="2cdc1-332">Ссылка, созданная с помощью `<a asp-page="/Manage/About">` будет правильной, только если последний запрос был к странице в области `Services`.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="2cdc1-333">Например, `/Services/Manage/`, `/Services/Manage/Index` или `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="2cdc1-334">Ссылка, созданная с помощью `<a asp-page="/About">` будет правильной, только если последний запрос был к странице в области `/Home`.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="2cdc1-335">Это код из [этого примера](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="2cdc1-336">Импорт пространства имен и вспомогательных функций тегов с помощью файла _ViewImports</span><span class="sxs-lookup"><span data-stu-id="2cdc1-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="2cdc1-337">Файл *_ViewImports. cshtml* можно добавить в каждую папку *страниц* с областями для импорта пространства имен и вспомогательных функций тегов на каждую Razor страницу в папке.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="2cdc1-338">Рассмотрите область *служб* в примере кода, которая не содержит файл *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="2cdc1-339">Следующая разметка показывает страницу */сервицес/манаже/абаут* Razor :</span><span class="sxs-lookup"><span data-stu-id="2cdc1-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="2cdc1-340">В этой разметке:</span><span class="sxs-lookup"><span data-stu-id="2cdc1-340">In the preceding markup:</span></span>

* <span data-ttu-id="2cdc1-341">Для указания модели (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`) необходимо использовать полное доменное имя.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="2cdc1-342">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) включены `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="2cdc1-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="2cdc1-343">В примере загрузка область продуктов содержит следующий файл *_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2cdc1-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="2cdc1-344">Следующая разметка показывает страницу */Продуктс/абаут* Razor :</span><span class="sxs-lookup"><span data-stu-id="2cdc1-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="2cdc1-345">В предыдущем файле пространство имен и директива `@addTagHelper` импортируются в файл с помощью файла *Areas/Products/Pages/_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2cdc1-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="2cdc1-346">Дополнительные сведения см. в разделах [Управление областью действия вспомогательной функции тега](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) и [Импорт общих директив](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="2cdc1-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="2cdc1-347">Общий макет для Razor областей страниц</span><span class="sxs-lookup"><span data-stu-id="2cdc1-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="2cdc1-348">Чтобы совместно использовать общий макет для всего приложения, переместите *_ViewStart.cshtml* в корневую папку приложения.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="2cdc1-349">Публикация областей</span><span class="sxs-lookup"><span data-stu-id="2cdc1-349">Publishing Areas</span></span>

<span data-ttu-id="2cdc1-350">Все файлы CSHTML и файлы в каталоге *wwwroot* публикуются в выходных данных, если в файл CSPROJ включен `<Project Sdk="Microsoft.NET.Sdk.Web">`.</span><span class="sxs-lookup"><span data-stu-id="2cdc1-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
