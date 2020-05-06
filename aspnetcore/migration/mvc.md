---
title: Миграция с ASP.NET MVC на ASP.NET Core MVC
author: ardalis
description: Узнайте, как приступить к переносу проекта ASP.NET MVC для ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777050"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="28b52-103">Миграция с ASP.NET MVC на ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="28b52-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="28b52-104">[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Даниэль Roth)](https://github.com/danroth27), [Стив Смит](https://ardalis.com/)и [Скотт Эдди (](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="28b52-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="28b52-105">В этой статье показано, как приступить к переносу проекта ASP.NET MVC для [ASP.NET Core MVC](../mvc/overview.md).</span><span class="sxs-lookup"><span data-stu-id="28b52-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="28b52-106">В процессе он выделяет многие вещи, которые изменились с ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="28b52-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="28b52-107">Миграция с ASP.NET MVC — это несколько этапов, и в этой статье рассматривается начальная настройка, базовые контроллеры и представления, статическое содержимое и зависимости на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="28b52-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="28b52-108">Дополнительные статьи охватывают перенос конфигурации и кода идентификации во многих проектах ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="28b52-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="28b52-109">Номера версий в примерах могут быть неактуальными.</span><span class="sxs-lookup"><span data-stu-id="28b52-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="28b52-110">Может потребоваться соответствующим образом обновить проекты.</span><span class="sxs-lookup"><span data-stu-id="28b52-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="28b52-111">Создание проекта MVC для начального ASP.NET</span><span class="sxs-lookup"><span data-stu-id="28b52-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="28b52-112">Чтобы продемонстрировать обновление, мы начнем с создания приложения ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="28b52-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="28b52-113">Создайте его с именем "имя *APP1* ", чтобы пространство имен совпадало с ASP.NET Core проектом, созданным на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="28b52-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Диалоговое окно «Создание проекта» Visual Studio](mvc/_static/new-project.png)

![Диалоговое окно "новое веб-приложение": шаблон проекта MVC, выбранный на панели шаблоны ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="28b52-116">*Необязательно:* Измените имя решения с *Mvc5* *на.*</span><span class="sxs-lookup"><span data-stu-id="28b52-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="28b52-117">Visual Studio отображает новое имя решения (*Mvc5*), которое упрощает указание этого проекта из следующего проекта.</span><span class="sxs-lookup"><span data-stu-id="28b52-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="28b52-118">Создание проекта ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28b52-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="28b52-119">Создайте *пустое* веб-приложение ASP.NET Core с тем же именем, что и у предыдущего проекта (веб-приложения*APP1*), чтобы пространства имен в двух проектах совпадали.</span><span class="sxs-lookup"><span data-stu-id="28b52-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="28b52-120">Наличие одного и того же пространства имен упрощает копирование кода между двумя проектами.</span><span class="sxs-lookup"><span data-stu-id="28b52-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="28b52-121">Вам потребуется создать этот проект в каталоге, отличном от каталога предыдущего проекта, для использования того же имени.</span><span class="sxs-lookup"><span data-stu-id="28b52-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Диалоговое окно "Новый проект"](mvc/_static/new_core.png)

![Диалоговое окно "Создание веб-приложения ASP.NET": пустой шаблон проекта, выбранный на панели "шаблоны ASP.NET Core"](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="28b52-124">*Необязательно:* Создайте новое приложение ASP.NET Core с помощью шаблона проекта *веб-приложения* .</span><span class="sxs-lookup"><span data-stu-id="28b52-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="28b52-125">Назовите проект *"* имя_проекта" и выберите параметр проверки подлинности для **отдельных учетных записей пользователей**.</span><span class="sxs-lookup"><span data-stu-id="28b52-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="28b52-126">Переименуйте это приложение в *фулласпнеткоре*.</span><span class="sxs-lookup"><span data-stu-id="28b52-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="28b52-127">Создание этого проекта экономит время при преобразовании.</span><span class="sxs-lookup"><span data-stu-id="28b52-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="28b52-128">Чтобы увидеть конечный результат или скопировать код в проект преобразования, можно взглянуть на созданный шаблоном код.</span><span class="sxs-lookup"><span data-stu-id="28b52-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="28b52-129">Это также полезно, если вы захотите выполнить шаг преобразования для сравнения с проектом, созданным с помощью шаблона.</span><span class="sxs-lookup"><span data-stu-id="28b52-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="28b52-130">Настройка сайта для использования MVC</span><span class="sxs-lookup"><span data-stu-id="28b52-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="28b52-131">При разработке для .NET Core ссылка на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) используется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="28b52-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="28b52-132">Этот пакет содержит пакеты, часто используемые приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="28b52-132">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="28b52-133">Если целевой объект .NET Framework, ссылки на пакеты должны быть перечислены по отдельности в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="28b52-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="28b52-134">При разработке для .NET Core ссылка на [Microsoft. AspNetCore. ALL метапакет](xref:fundamentals/metapackage) используется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="28b52-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="28b52-135">Этот пакет содержит пакеты, часто используемые пакетами приложений MVC.</span><span class="sxs-lookup"><span data-stu-id="28b52-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="28b52-136">Если целевой объект .NET Framework, ссылки на пакеты должны быть перечислены по отдельности в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="28b52-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="28b52-137">При нацеливании на .NET Core или .NET Framework пакеты, обычно используемые приложениями MVC, перечисляются по отдельности в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="28b52-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="28b52-138">`Microsoft.AspNetCore.Mvc`является ASP.NET Core платформой MVC.</span><span class="sxs-lookup"><span data-stu-id="28b52-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="28b52-139">`Microsoft.AspNetCore.StaticFiles`является обработчиком статических файлов.</span><span class="sxs-lookup"><span data-stu-id="28b52-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="28b52-140">Среда выполнения ASP.NET Core является модульной, и необходимо явно включить для обслуживания статических файлов (см. раздел [статические файлы](xref:fundamentals/static-files)).</span><span class="sxs-lookup"><span data-stu-id="28b52-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="28b52-141">Откройте файл *Startup.CS* и измените код в соответствии со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="28b52-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="28b52-142">Метод `UseStaticFiles` расширения добавляет обработчик статических файлов.</span><span class="sxs-lookup"><span data-stu-id="28b52-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="28b52-143">Как упоминалось ранее, среда выполнения ASP.NET является модульной, и необходимо явно использовать для обслуживания статических файлов.</span><span class="sxs-lookup"><span data-stu-id="28b52-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="28b52-144">Метод `UseMvc` расширения добавляет маршрутизацию.</span><span class="sxs-lookup"><span data-stu-id="28b52-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="28b52-145">Дополнительные сведения см. в разделе Запуск и [Маршрутизация](xref:fundamentals/routing) [приложений](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="28b52-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="28b52-146">Добавление контроллера и представления</span><span class="sxs-lookup"><span data-stu-id="28b52-146">Add a controller and view</span></span>

<span data-ttu-id="28b52-147">В этом разделе вы добавите минимальный контроллер и представление, которые будут использоваться в качестве заполнителей для контроллера MVC ASP.NET и представлений, которые вы будете переносить в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="28b52-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="28b52-148">Добавьте папку *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="28b52-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="28b52-149">Добавьте **класс контроллера** с именем *HomeController.CS* в папку *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="28b52-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![Диалоговое окно ''Добавление нового элемента''](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="28b52-151">Добавьте папку *views* .</span><span class="sxs-lookup"><span data-stu-id="28b52-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="28b52-152">Добавьте папки *Views/Home* .</span><span class="sxs-lookup"><span data-stu-id="28b52-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="28b52-153">Добавьте \*\* Razor представление\*\* с именем *index. cshtml* в папку *Views/Home* .</span><span class="sxs-lookup"><span data-stu-id="28b52-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![Диалоговое окно ''Добавление нового элемента''](mvc/_static/view.png)

<span data-ttu-id="28b52-155">Структура проекта показана ниже:</span><span class="sxs-lookup"><span data-stu-id="28b52-155">The project structure is shown below:</span></span>

![обозреватель решений отображения файлов и папок в файле App1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="28b52-157">Замените содержимое файла *Views/Home/Index. cshtml* следующим:</span><span class="sxs-lookup"><span data-stu-id="28b52-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="28b52-158">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="28b52-158">Run the app.</span></span>

![Веб-приложение открыто в Microsoft ребро](mvc/_static/hello-world.png)

<span data-ttu-id="28b52-160">Дополнительные сведения см. в разделе [Controllers](xref:mvc/controllers/actions) and [views](xref:mvc/views/overview) .</span><span class="sxs-lookup"><span data-stu-id="28b52-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="28b52-161">Теперь, когда у нас есть проект с минимальными рабочими ASP.NET Core, мы можем начать миграцию функций из проекта ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="28b52-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="28b52-162">Необходимо переместить следующее:</span><span class="sxs-lookup"><span data-stu-id="28b52-162">We need to move the following:</span></span>

* <span data-ttu-id="28b52-163">содержимое на стороне клиента (CSS, шрифты и скрипты)</span><span class="sxs-lookup"><span data-stu-id="28b52-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="28b52-164">controllers</span><span class="sxs-lookup"><span data-stu-id="28b52-164">controllers</span></span>

* <span data-ttu-id="28b52-165">узел "Представления"</span><span class="sxs-lookup"><span data-stu-id="28b52-165">views</span></span>

* <span data-ttu-id="28b52-166">модели</span><span class="sxs-lookup"><span data-stu-id="28b52-166">models</span></span>

* <span data-ttu-id="28b52-167">объединения</span><span class="sxs-lookup"><span data-stu-id="28b52-167">bundling</span></span>

* <span data-ttu-id="28b52-168">filters</span><span class="sxs-lookup"><span data-stu-id="28b52-168">filters</span></span>

* <span data-ttu-id="28b52-169">Вход в систему Identity (это делается в следующем руководстве).</span><span class="sxs-lookup"><span data-stu-id="28b52-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="28b52-170">Контроллеры и представления</span><span class="sxs-lookup"><span data-stu-id="28b52-170">Controllers and views</span></span>

* <span data-ttu-id="28b52-171">Скопируйте каждый из методов из MVC `HomeController` ASP.NET в новый. `HomeController`</span><span class="sxs-lookup"><span data-stu-id="28b52-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="28b52-172">Обратите внимание, что в ASP.NET MVC тип возвращаемого значения метода действия контроллера встроенного шаблона — [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); в ASP.NET Core MVC вместо этого возвращаются `IActionResult` методы действия.</span><span class="sxs-lookup"><span data-stu-id="28b52-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="28b52-173">`ActionResult`реализует `IActionResult`, поэтому нет необходимости изменять тип возвращаемого значения методов действия.</span><span class="sxs-lookup"><span data-stu-id="28b52-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="28b52-174">Скопируйте файлы представления *About. cshtml*, *Contact. cshtml*и *index. cshtml* Razor из проекта MVC ASP.NET в проект ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28b52-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="28b52-175">Запустите приложение ASP.NET Core и протестируйте каждый метод.</span><span class="sxs-lookup"><span data-stu-id="28b52-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="28b52-176">Мы еще не выполнили миграцию файла макета или стилей, поэтому отображаемые представления содержат только содержимое в файлах представления.</span><span class="sxs-lookup"><span data-stu-id="28b52-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="28b52-177">В файле макета не будут созданы ссылки для представлений `About` и `Contact` , поэтому их придется вызывать из браузера (замените **4492** на номер порта, используемый в проекте).</span><span class="sxs-lookup"><span data-stu-id="28b52-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Страница контактов](mvc/_static/contact-page.png)

<span data-ttu-id="28b52-179">Обратите внимание на отсутствие стилей и пунктов меню.</span><span class="sxs-lookup"><span data-stu-id="28b52-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="28b52-180">Мы исправим это в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="28b52-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="28b52-181">Статическое содержимое</span><span class="sxs-lookup"><span data-stu-id="28b52-181">Static content</span></span>

<span data-ttu-id="28b52-182">В предыдущих версиях ASP.NET MVC статическое содержимое было размещено из корневого каталога веб-проекта и было смешано с файлами на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="28b52-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="28b52-183">В ASP.NET Core статическое содержимое размещается в папке *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="28b52-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="28b52-184">Вам потребуется скопировать статическое содержимое из старого приложения ASP.NET MVC в папку *wwwroot* в проекте ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28b52-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="28b52-185">В этом примере преобразования:</span><span class="sxs-lookup"><span data-stu-id="28b52-185">In this sample conversion:</span></span>

* <span data-ttu-id="28b52-186">Скопируйте файл *фавикон. ico* из старого проекта MVC в папку *wwwroot* в проекте ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28b52-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="28b52-187">Старый проект ASP.NET MVC использует для своей стилизации [начальную загрузку](https://getbootstrap.com/) и сохраняет файлы начальной загрузки в папках *Content* и *Scripts* .</span><span class="sxs-lookup"><span data-stu-id="28b52-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="28b52-188">Шаблон, создавший старый проект MVC ASP.NET, ссылается на загрузку в файле макета (*views/Shared/_layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="28b52-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="28b52-189">Можно скопировать файлы *начальной загрузки. js* и *начальной загрузки css* из проекта ASP.NET MVC в папку *wwwroot* в новом проекте.</span><span class="sxs-lookup"><span data-stu-id="28b52-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="28b52-190">Вместо этого мы добавим поддержку начальной загрузки (и других клиентских библиотек) с помощью CDN в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="28b52-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="28b52-191">Перенос файла макета</span><span class="sxs-lookup"><span data-stu-id="28b52-191">Migrate the layout file</span></span>

* <span data-ttu-id="28b52-192">Скопируйте файл *_ViewStart. cshtml* из папки *views* старого проекта ASP.NET MVC в папку *views* проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28b52-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="28b52-193">Файл *_ViewStart. cshtml* не был изменен в ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="28b52-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="28b52-194">Создайте папку *views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="28b52-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="28b52-195">*Необязательно:* Скопируйте *_ViewImports. cshtml* из папки *views* проекта *фулласпнеткоре* MVC в папку *представления* проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28b52-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="28b52-196">Удалите любое объявление пространства имен в файле *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="28b52-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="28b52-197">Файл *_ViewImports. cshtml* предоставляет пространства имен для всех файлов представлений и переводит их в [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="28b52-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="28b52-198">Вспомогательные функции тегов используются в новом файле макета.</span><span class="sxs-lookup"><span data-stu-id="28b52-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="28b52-199">Файл *_ViewImports. cshtml* является новым для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28b52-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="28b52-200">Скопируйте файл *_layout. cshtml* из старого *представления или общей* папки проекта MVC ASP.NET в папку *views/Shared (представления* ) проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28b52-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="28b52-201">Откройте файл *_layout. cshtml* и внесите следующие изменения (завершенный код показан ниже):</span><span class="sxs-lookup"><span data-stu-id="28b52-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="28b52-202">Замените `@Styles.Render("~/Content/css")` на `<link>` элемент для загрузки *начальной загрузки. CSS* (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="28b52-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="28b52-203">Удалите `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="28b52-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="28b52-204">Закомментируйте `@Html.Partial("_LoginPartial")` строку (заключите строку в `@*...*@`).</span><span class="sxs-lookup"><span data-stu-id="28b52-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="28b52-205">Дополнительные сведения см. [в статье миграция проверки подлинности и Identity для ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="28b52-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="28b52-206">Замените `@Scripts.Render("~/bundles/jquery")` на `<script>` элемент (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="28b52-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="28b52-207">Замените `@Scripts.Render("~/bundles/bootstrap")` на `<script>` элемент (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="28b52-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="28b52-208">Заменяющая разметка для включения начальной загрузки CSS:</span><span class="sxs-lookup"><span data-stu-id="28b52-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="28b52-209">Заменяющая разметка для jQuery и начальная загрузка JavaScript:</span><span class="sxs-lookup"><span data-stu-id="28b52-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="28b52-210">Обновленный файл *_layout. cshtml* показан ниже:</span><span class="sxs-lookup"><span data-stu-id="28b52-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="28b52-211">Просмотр сайта в браузере.</span><span class="sxs-lookup"><span data-stu-id="28b52-211">View the site in the browser.</span></span> <span data-ttu-id="28b52-212">Теперь она должна быть правильно загружена с ожидаемыми стилями.</span><span class="sxs-lookup"><span data-stu-id="28b52-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="28b52-213">*Необязательно:* Может потребоваться использовать новый файл макета.</span><span class="sxs-lookup"><span data-stu-id="28b52-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="28b52-214">Для этого проекта можно скопировать файл макета из проекта *фулласпнеткоре* .</span><span class="sxs-lookup"><span data-stu-id="28b52-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="28b52-215">Новый файл макета использует [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и содержит другие улучшения.</span><span class="sxs-lookup"><span data-stu-id="28b52-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="28b52-216">Настройка объединения и минификации</span><span class="sxs-lookup"><span data-stu-id="28b52-216">Configure bundling and minification</span></span>

<span data-ttu-id="28b52-217">Сведения о настройке объединения и минификации см. в разделе [объединение и минификации](../client-side/bundling-and-minification.md).</span><span class="sxs-lookup"><span data-stu-id="28b52-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="28b52-218">Устранение ошибок HTTP 500</span><span class="sxs-lookup"><span data-stu-id="28b52-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="28b52-219">Существует множество проблем, которые могут вызвать сообщение об ошибке HTTP 500, которое не содержит информации об источнике проблемы.</span><span class="sxs-lookup"><span data-stu-id="28b52-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="28b52-220">Например, если файл *views/_ViewImports. cshtml* содержит пространство имен, которое не существует в проекте, вы получите ошибку HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="28b52-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="28b52-221">По умолчанию в ASP.NET Core приложениях `UseDeveloperExceptionPage` расширение добавляется в `IApplicationBuilder` и выполняется при *разработке*конфигурации.</span><span class="sxs-lookup"><span data-stu-id="28b52-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="28b52-222">Это подробно описано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="28b52-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="28b52-223">ASP.NET Core преобразует необработанные исключения в веб-приложении в ответные сообщения об ошибках HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="28b52-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="28b52-224">Как правило, сведения об ошибке не включаются в эти ответы, чтобы предотвратить раскрытие потенциально конфиденциальной информации о сервере.</span><span class="sxs-lookup"><span data-stu-id="28b52-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="28b52-225">Дополнительные сведения см. в статье об **использовании страницы "исключение разработчика"** в разделе " [ошибки обработчика](../fundamentals/error-handling.md) ".</span><span class="sxs-lookup"><span data-stu-id="28b52-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28b52-226">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="28b52-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
