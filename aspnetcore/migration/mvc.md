---
title: Научитесь переходить с ASP.NET MVC на ASP.NET Core MVC
author: wadepickett
description: Узнайте, как начать миграцию проекта MVC ASP.NET в ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: d615f67fc5cb23499ee7e14b747390a7a1b5a693
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865135"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="1bb4e-103">Миграция с ASP.NET MVC на ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="1bb4e-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1bb4e-104">В этой статье показано, как начать миграцию проекта ASP.NET MVC в [ASP.NET Core MVC](xref:mvc/overview).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="1bb4e-105">В процессе он выделяет связанные изменения из ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="1bb4e-106">Миграция с ASP.NET MVC — это многоэтапный процесс.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="1bb4e-107">В этой статье рассматриваются следующие вопросы:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-107">This article covers:</span></span>

* <span data-ttu-id="1bb4e-108">Начальная настройка.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-108">Initial setup.</span></span>
* <span data-ttu-id="1bb4e-109">Основные контроллеры и представления.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-109">Basic controllers and views.</span></span>
* <span data-ttu-id="1bb4e-110">Статическое содержимое.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-110">Static content.</span></span>
* <span data-ttu-id="1bb4e-111">Зависимости на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-111">Client-side dependencies.</span></span>

<span data-ttu-id="1bb4e-112">Сведения о переносе конфигурации и Identity кода см. в разделе [Миграция конфигурации в ASP.NET Core](xref:migration/configuration) и [Миграция проверки подлинности и Identity ASP.NET Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-112">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1bb4e-113">Обязательные условия</span><span class="sxs-lookup"><span data-stu-id="1bb4e-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="1bb4e-114">Создание проекта MVC для начального ASP.NET</span><span class="sxs-lookup"><span data-stu-id="1bb4e-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="1bb4e-115">Создайте пример проекта MVC ASP.NET в Visual Studio, чтобы выполнить миграцию:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="1bb4e-116">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-116">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="1bb4e-117">Выберите **веб-приложение ASP.NET (.NET Framework)** , а затем нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next**.</span></span>
1. <span data-ttu-id="1bb4e-118">Присвойте проекту имя *APP1* , чтобы пространство имен совпадало с ASP.NET Core проектом, созданным на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="1bb4e-119">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-119">Select **Create**.</span></span>
1. <span data-ttu-id="1bb4e-120">Выберите **MVC**и нажмите кнопку **создать**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-120">Select **MVC**, and then select **Create**.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="1bb4e-121">Создание проекта ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1bb4e-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="1bb4e-122">Создайте новое решение с новым ASP.NET Core проектом, на который будет осуществляться миграция:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="1bb4e-123">Запустите второй экземпляр Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="1bb4e-124">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-124">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="1bb4e-125">Выберите **веб-приложение ASP.NET Web Core** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-125">Select **ASP.NET Web Core Web Application** and then select **Next**.</span></span>
1. <span data-ttu-id="1bb4e-126">В диалоговом окне **Настройка нового проекта** присвойте проекту имя *APP1*.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-126">In the **Configure your new project** dialog, Name the project *WebApp1*.</span></span>
1. <span data-ttu-id="1bb4e-127">В качестве расположения укажите каталог, отличный от каталога предыдущего проекта, для использования того же имени проекта.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="1bb4e-128">Использование одного и того же пространства имен упрощает копирование кода между двумя проектами.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="1bb4e-129">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-129">Select **Create**.</span></span>
1. <span data-ttu-id="1bb4e-130">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="1bb4e-131">Выберите шаблон проекта **веб-приложение (модель-представление-контроллер)** и щелкните **создать**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create**.</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="1bb4e-132">Настройка сайта ASP.NET Core для использования MVC</span><span class="sxs-lookup"><span data-stu-id="1bb4e-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="1bb4e-133">В проектах ASP.NET Core 3,0 и более поздних версий .NET Framework больше не является поддерживаемой целевой платформой.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="1bb4e-134">Проект должен быть предназначен для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-134">Your project must target .NET Core.</span></span> <span data-ttu-id="1bb4e-135">ASP.NET Core общая платформа, которая включает MVC, является частью установки среды выполнения .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="1bb4e-136">При использовании пакета SDK `Microsoft.NET.Sdk.Web` в файле проекта автоматически создается ссылка на общую платформу:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="1bb4e-137">Дополнительные сведения см. в разделе [Справочник по платформе](xref:migration/22-to-30#framework-reference).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="1bb4e-138">В ASP.NET Core `Startup` класс:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="1bb4e-139">Заменяет *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="1bb4e-140">Обрабатывает все задачи запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="1bb4e-141">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="1bb4e-142">В проекте ASP.NET Core откройте файл *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="1bb4e-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="1bb4e-143">ASP.NET Core приложения должны участвовать в функциях платформы с по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="1bb4e-144">Предыдущий сформированный шаблоном код добавляет следующие службы и по промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="1bb4e-145"><xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>Метод расширения регистрирует поддержку служб MVC для контроллеров, функций, связанных с API, и представлений.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="1bb4e-146">Дополнительные сведения о параметрах регистрации службы MVC см. в разделе [Регистрация службы MVC](xref:migration/22-to-30#mvc-service-registration) .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="1bb4e-147"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>Метод расширения добавляет обработчик статических файлов `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="1bb4e-148">`UseStaticFiles`Метод расширения должен быть вызван раньше `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="1bb4e-149">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="1bb4e-150"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>Метод расширения добавляет маршрутизацию.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="1bb4e-151">Для получения дополнительной информации см. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="1bb4e-152">Эта существующая конфигурация включает в себя, что необходимо для переноса примера проекта MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="1bb4e-153">Дополнительные сведения о параметрах по промежуточного слоя ASP.NET Core см. в разделе <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="1bb4e-154">Миграция контроллеров и представлений</span><span class="sxs-lookup"><span data-stu-id="1bb4e-154">Migrate controllers and views</span></span>

<span data-ttu-id="1bb4e-155">В проекте ASP.NET Core будет добавлен новый пустой класс контроллера и класс представления, которые будут использоваться в качестве заполнителей с теми же именами, что и у контроллера, и классов представлений в любом проекте MVC ASP.NET, из которого выполняется миграция.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="1bb4e-156">Проект ASP.NET Core. *APP1* уже содержит минимальный пример контроллера и представления с тем же именем, что и проект MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="1bb4e-157">Таким образом, они будут служить заполнителями для контроллера MVC ASP.NET и представлений, которые будут перенесены *из проекта ASP.NET* MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="1bb4e-158">Скопируйте методы из ASP.NET MVC, `HomeController` чтобы заменить новые `HomeController` методы ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="1bb4e-159">Нет необходимости изменять тип возвращаемого значения методов действия.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="1bb4e-160">Тип возвращаемого значения метода действия контроллера встроенного шаблона ASP.NET MVC — <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; в ASP.NET Core MVC вместо этого методы действия возвращают `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-160">The ASP.NET MVC built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="1bb4e-161">Объект `ActionResult` реализует интерфейс `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="1bb4e-162">В проекте ASP.NET Core щелкните правой кнопкой мыши папку *Views/Home* и выберите **Добавить** > **существующий элемент**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="1bb4e-163">В диалоговом окне **Добавление существующего элемента** перейдите к *представлениям/домашнему* каталогу проекта ASP.NET MVC *APP1* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="1bb4e-164">Выберите файлы представлений *About. cshtml*, *Contact. cshtml*и *index. cshtml* Razor , а затем щелкните **Добавить**, заменив существующие файлы.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-164">Select the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files, then select **Add**, replacing the existing files.</span></span>

<span data-ttu-id="1bb4e-165">Дополнительные сведения см. в разделах <xref:mvc/controllers/actions> и <xref:mvc/views/overview>.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="1bb4e-166">Тестирование каждого метода</span><span class="sxs-lookup"><span data-stu-id="1bb4e-166">Test each method</span></span>

<span data-ttu-id="1bb4e-167">Каждая конечная точка контроллера может быть протестирована, однако макет и стили рассматриваются далее в документе.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="1bb4e-168">Запустите приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="1bb4e-169">Вызовите готовые представления из браузера в работающем ASP.NET Core приложении, заменив текущий номер порта на номер порта, используемый в проекте ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="1bb4e-170">Например, `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="1bb4e-171">Миграция статического содержимого</span><span class="sxs-lookup"><span data-stu-id="1bb4e-171">Migrate static content</span></span>

<span data-ttu-id="1bb4e-172">В ASP.NET MVC 5 и более ранних версиях статическое содержимое было размещено из корневого каталога веб-проекта и было смешано с файлами на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="1bb4e-173">В ASP.NET Core статические файлы хранятся в [корневом каталоге веб-сайта](xref:fundamentals/index#web-root) проекта.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="1bb4e-174">Каталогом по умолчанию является *{Content root}/ввврут*, но его можно изменить.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-174">The default directory is *{content root}/wwwroot*, but it can be changed.</span></span> <span data-ttu-id="1bb4e-175">Подробные сведения см. в статье [Статические файлы в ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="1bb4e-176">Скопируйте статическое содержимое из проекта ASP.NET MVC *APP1* в каталог *wwwroot* в *проекте ASP.NET Core.*</span><span class="sxs-lookup"><span data-stu-id="1bb4e-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="1bb4e-177">В проекте ASP.NET Core щелкните правой кнопкой мыши каталог *wwwroot* и выберите **Добавить** > **существующий элемент**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="1bb4e-178">В диалоговом окне **Добавление существующего элемента** перейдите *к проекту ASP.NET* MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="1bb4e-179">Выберите файл *фавикон. ico* , а затем нажмите кнопку **Добавить**, заменив существующий файл.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-179">Select the *favicon.ico* file, then select **Add**, replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="1bb4e-180">Перенос файлов макета</span><span class="sxs-lookup"><span data-stu-id="1bb4e-180">Migrate the layout files</span></span>

<span data-ttu-id="1bb4e-181">Скопируйте файлы макета проекта ASP.NET MVC в проект ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="1bb4e-182">В проекте ASP.NET Core щелкните правой кнопкой мыши каталог *views* и выберите **Добавить** > **существующий элемент**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="1bb4e-183">В диалоговом окне **Добавление существующего элемента** перейдите к каталогу *views* The *ASP.NET MVC Project* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="1bb4e-184">Выберите файл *_ViewStart. cshtml* и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-184">Select the *_ViewStart.cshtml* file then select **Add**.</span></span>

<span data-ttu-id="1bb4e-185">Скопируйте файлы общего макета проекта ASP.NET MVC в проект ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="1bb4e-186">В проекте ASP.NET Core щелкните правой кнопкой мыши элемент *представления или общий* каталог и выберите команду **Добавить** > **существующий элемент**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="1bb4e-187">В диалоговом окне **Добавление существующего элемента** перейдите к *представлениям/общему* каталогу проекта ASP.NET MVC *APP1* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="1bb4e-188">Выберите файл *_layout. cshtml* , а затем нажмите кнопку **Добавить**, заменив существующий файл.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-188">Select the *_Layout.cshtml* file, then select **Add**, replacing the existing file.</span></span>

<span data-ttu-id="1bb4e-189">В проекте ASP.NET Core откройте файл *_layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="1bb4e-190">Внесите следующие изменения в соответствии с завершенным кодом, показанным ниже:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="1bb4e-191">Обновите включение начальной загрузки CSS в соответствии с завершенным кодом ниже:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="1bb4e-192">Замените на `@Styles.Render("~/Content/css")` `<link>` элемент для загрузки *начальной загрузки. CSS* (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="1bb4e-193">Удалите `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="1bb4e-194">Завершенная замещенная разметка для включения начальной загрузки CSS:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="1bb4e-195">Обновите включение jQuery и начальную загрузку JavaScript в соответствии с завершенным кодом ниже:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="1bb4e-196">Замените на `@Scripts.Render("~/bundles/jquery")` `<script>` элемент (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="1bb4e-197">Замените на `@Scripts.Render("~/bundles/bootstrap")` `<script>` элемент (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="1bb4e-198">Завершенная замещающая разметка для jQuery и начальная загрузка JavaScript:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="1bb4e-199">Обновленный файл *_layout. cshtml* показан ниже:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="1bb4e-200">Просмотр сайта в браузере.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-200">View the site in the browser.</span></span> <span data-ttu-id="1bb4e-201">Он должен отображаться с ожидаемыми стилями на месте.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="1bb4e-202">Настройка объединения и минификации</span><span class="sxs-lookup"><span data-stu-id="1bb4e-202">Configure bundling and minification</span></span>

<span data-ttu-id="1bb4e-203">ASP.NET Core совместим с несколькими решениями для объединения с открытым кодом и минификации, такими как [оптимизатор](https://github.com/ligershark/WebOptimizer) и другие аналогичные библиотеки.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="1bb4e-204">ASP.NET Core не предоставляет собственное решение для объединения и минификации.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="1bb4e-205">Сведения о настройке объединения и минификации см. в разделе [объединение и минификации](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="1bb4e-206">Устранение ошибок HTTP 500</span><span class="sxs-lookup"><span data-stu-id="1bb4e-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="1bb4e-207">Существует множество проблем, которые могут вызвать сообщение об ошибке HTTP 500, которое не содержит информации об источнике проблемы.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="1bb4e-208">Например, если файл *views/_ViewImports. cshtml* содержит пространство имен, которое не существует в проекте, создается ошибка HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="1bb4e-209">По умолчанию в ASP.NET Core приложениях `UseDeveloperExceptionPage` расширение добавляется в `IApplicationBuilder` и выполняется при *разработке*среды.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development*.</span></span> <span data-ttu-id="1bb4e-210">Это подробно описано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="1bb4e-211">ASP.NET Core преобразует необработанные исключения в отклики об ошибках HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="1bb4e-212">Как правило, сведения об ошибке не включаются в эти ответы, чтобы предотвратить раскрытие потенциально конфиденциальной информации о сервере.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="1bb4e-213">Дополнительные сведения см. в разделе [Страница "исключение разработчика](xref:fundamentals/error-handling#developer-exception-page)".</span><span class="sxs-lookup"><span data-stu-id="1bb4e-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="1bb4e-214">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="1bb4e-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="1bb4e-215">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1bb4e-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1bb4e-216">В этой статье показано, как начать перенос проекта MVC ASP.NET в [ASP.NET Core MVC](xref:mvc/overview) 2,2.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="1bb4e-217">В процессе он выделяет многие вещи, которые изменились с ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="1bb4e-218">Миграция с ASP.NET MVC — это многоэтапный процесс.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="1bb4e-219">В этой статье рассматриваются следующие вопросы:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-219">This article covers:</span></span>

* <span data-ttu-id="1bb4e-220">Начальная настройка</span><span class="sxs-lookup"><span data-stu-id="1bb4e-220">Initial setup</span></span>
* <span data-ttu-id="1bb4e-221">Основные контроллеры и представления</span><span class="sxs-lookup"><span data-stu-id="1bb4e-221">Basic controllers and views</span></span>
* <span data-ttu-id="1bb4e-222">Статическое содержимое</span><span class="sxs-lookup"><span data-stu-id="1bb4e-222">Static content</span></span>
* <span data-ttu-id="1bb4e-223">Зависимости на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-223">Client-side dependencies.</span></span>

<span data-ttu-id="1bb4e-224">Сведения о переносе конфигурации и Identity кода см. в разделе <xref:migration/configuration> и <xref:migration/identity> .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-224">For migrating configuration and Identity code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="1bb4e-225">Номера версий в примерах могут быть неактуальными. Обновите проекты соответствующим образом.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="1bb4e-226">Создание проекта MVC для начального ASP.NET</span><span class="sxs-lookup"><span data-stu-id="1bb4e-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="1bb4e-227">Чтобы продемонстрировать обновление, мы начнем с создания приложения ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="1bb4e-228">Создайте его с именем "имя *APP1* ", чтобы пространство имен совпадало с ASP.NET Core проектом, созданным на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Диалоговое окно «Создание проекта» Visual Studio](mvc/_static/new-project.png)

![Диалоговое окно "новое веб-приложение": шаблон проекта MVC, выбранный на панели шаблоны ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="1bb4e-231">*Необязательно:* Измените имя решения с *Mvc5* *на.*</span><span class="sxs-lookup"><span data-stu-id="1bb4e-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="1bb4e-232">Visual Studio отображает новое имя решения (*Mvc5*), которое упрощает указание этого проекта из следующего проекта.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-232">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="1bb4e-233">Создание проекта ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1bb4e-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="1bb4e-234">Создайте *пустое* веб-приложение ASP.NET Core с тем же именем, что и у предыдущего проекта (веб-приложения*APP1*), чтобы пространства имен в двух проектах совпадали.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="1bb4e-235">Наличие одного и того же пространства имен упрощает копирование кода между двумя проектами.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="1bb4e-236">Создайте этот проект в каталоге, отличном от каталога предыдущего проекта, для использования того же имени.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![Диалоговое окно "Новый проект"](mvc/_static/new_core.png)

![Диалоговое окно "Создание веб-приложения ASP.NET": пустой шаблон проекта, выбранный на панели "шаблоны ASP.NET Core"](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="1bb4e-239">*Необязательно:* Создайте новое приложение ASP.NET Core с помощью шаблона проекта *веб-приложения* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="1bb4e-240">Назовите проект *"* имя_проекта" и выберите параметр проверки подлинности для **отдельных учетных записей пользователей**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-240">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="1bb4e-241">Переименуйте это приложение в *фулласпнеткоре*.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-241">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="1bb4e-242">Создание этого проекта экономит время в преобразовании.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="1bb4e-243">Конечный результат можно просмотреть в коде, созданном шаблоном. код можно скопировать в проект преобразования или сравнить с созданным шаблоном проектом.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="1bb4e-244">Настройка сайта для использования MVC</span><span class="sxs-lookup"><span data-stu-id="1bb4e-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="1bb4e-245">При разработке для .NET Core ссылка на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) используется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="1bb4e-246">Этот пакет содержит пакеты, часто используемые приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="1bb4e-247">Если целевой объект .NET Framework, ссылки на пакеты должны быть перечислены по отдельности в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="1bb4e-248">`Microsoft.AspNetCore.Mvc` является ASP.NET Core платформой MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="1bb4e-249">`Microsoft.AspNetCore.StaticFiles` является обработчиком статических файлов.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="1bb4e-250">ASP.NET Core приложения явно хотят использовать по промежуточного слоя, например для обслуживания статических файлов.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="1bb4e-251">Дополнительные сведения см. в разделе [Статические файлы](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="1bb4e-252">Откройте файл *Startup.CS* и измените код в соответствии со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="1bb4e-253"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Метод расширения добавляет обработчик статических файлов.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="1bb4e-254">Дополнительные сведения см. в разделе Запуск и [Маршрутизация](xref:fundamentals/routing) [приложений](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="1bb4e-255">Добавление контроллера и представления</span><span class="sxs-lookup"><span data-stu-id="1bb4e-255">Add a controller and view</span></span>

<span data-ttu-id="1bb4e-256">В этом разделе добавлены минимальный контроллер и представление, которые будут использоваться в качестве заполнителей для контроллера MVC ASP.NET и представлений, перенесенных в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="1bb4e-257">Добавьте каталог *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="1bb4e-258">Добавьте **класс контроллера** с именем *HomeController.CS* в каталог *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Диалоговое окно ''Добавление нового элемента''](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="1bb4e-260">Добавьте каталог *views* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="1bb4e-261">Добавление *представлений и домашнего* каталога.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="1bb4e-262">Добавьте \*\* Razor представление\*\* с именем *index. cshtml* в каталог *Views/Home* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-262">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Диалоговое окно ''Добавление нового элемента''](mvc/_static/view.png)

<span data-ttu-id="1bb4e-264">Структура проекта показана ниже:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-264">The project structure is shown below:</span></span>

![обозреватель решений отображения файлов и каталогов для файла App1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="1bb4e-266">Замените содержимое файла *Views/Home/Index.cshtml* следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="1bb4e-267">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-267">Run the app.</span></span>

![Веб-приложение открыто в Microsoft ребро](mvc/_static/hello-world.png)

<span data-ttu-id="1bb4e-269">Дополнительные сведения см. в разделе [Controllers](xref:mvc/controllers/actions) and [views](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="1bb4e-270">Следующие функции требуют миграции из примера проекта MVC ASP.NET в проект ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="1bb4e-271">содержимое на стороне клиента (CSS, шрифты и скрипты)</span><span class="sxs-lookup"><span data-stu-id="1bb4e-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="1bb4e-272">controllers</span><span class="sxs-lookup"><span data-stu-id="1bb4e-272">controllers</span></span>

* <span data-ttu-id="1bb4e-273">узел "Представления"</span><span class="sxs-lookup"><span data-stu-id="1bb4e-273">views</span></span>

* <span data-ttu-id="1bb4e-274">модели</span><span class="sxs-lookup"><span data-stu-id="1bb4e-274">models</span></span>

* <span data-ttu-id="1bb4e-275">объединения</span><span class="sxs-lookup"><span data-stu-id="1bb4e-275">bundling</span></span>

* <span data-ttu-id="1bb4e-276">filters</span><span class="sxs-lookup"><span data-stu-id="1bb4e-276">filters</span></span>

* <span data-ttu-id="1bb4e-277">Вход в систему Identity (это делается в следующем руководстве).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-277">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="1bb4e-278">Контроллеры и представления</span><span class="sxs-lookup"><span data-stu-id="1bb4e-278">Controllers and views</span></span>

* <span data-ttu-id="1bb4e-279">Скопируйте каждый из методов из MVC ASP.NET `HomeController` в новый `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="1bb4e-280">В ASP.NET MVC тип возвращаемого значения метода действия контроллера встроенного шаблона — <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; в ASP.NET Core MVC вместо этого методы действия возвращают `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-280">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="1bb4e-281">`ActionResult` реализует `IActionResult` , поэтому нет необходимости изменять тип возвращаемого значения методов действия.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="1bb4e-282">Скопируйте файлы представления *About. cshtml*, *Contact. cshtml*и *index. cshtml* Razor из проекта MVC ASP.NET в проект ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-282">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="1bb4e-283">Тестирование каждого метода</span><span class="sxs-lookup"><span data-stu-id="1bb4e-283">Test each method</span></span>

<span data-ttu-id="1bb4e-284">Файл макета и стили еще не перенесены, поэтому отображаемые представления содержат только содержимое в файлах представления.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="1bb4e-285">Файл макета, в котором созданы ссылки `About` для `Contact` представлений и, пока не будет доступен.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="1bb4e-286">Вызовите готовые представления из браузера в запущенном приложении ASP.NET Core, заменив текущий номер порта на номер порта, используемый в проекте ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="1bb4e-287">Например: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-287">For example: `https://localhost:44375/home/about`.</span></span>

![Страница контактов](mvc/_static/contact-page.png)

<span data-ttu-id="1bb4e-289">Обратите внимание на отсутствие стилей и пунктов меню.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="1bb4e-290">Стиль будет исправлен в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="1bb4e-291">Статическое содержимое</span><span class="sxs-lookup"><span data-stu-id="1bb4e-291">Static content</span></span>

<span data-ttu-id="1bb4e-292">В ASP.NET MVC 5 и более ранних версиях статическое содержимое было размещено из корневого каталога веб-проекта и было смешано с файлами на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="1bb4e-293">В ASP.NET Core статическое содержимое размещается в каталоге *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="1bb4e-294">Скопируйте статическое содержимое из приложения ASP.NET MVC в каталог *wwwroot* в проекте ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="1bb4e-295">В этом примере преобразования:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-295">In this sample conversion:</span></span>

* <span data-ttu-id="1bb4e-296">Скопируйте файл *фавикон. ico* из проекта MVC ASP.NET в каталог *wwwroot* в проекте ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="1bb4e-297">Проект ASP.NET MVC использует для своей стилизации [начальную загрузку](https://getbootstrap.com/) и сохраняет файлы начальной загрузки в каталогах *содержимого* и *скриптов* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="1bb4e-298">Шаблон, создавший проект MVC ASP.NET, ссылается на загрузку в файле макета (*views/Shared/_layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="1bb4e-299">Файлы *bootstrap.js* и *начальной загрузки CSS* можно скопировать из проекта MVC ASP.NET в каталог *wwwroot* в новом проекте.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="1bb4e-300">Вместо этого в этом документе добавляется поддержка начальной загрузки (и других клиентских библиотек) с помощью CDN в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="1bb4e-301">Перенос файла макета</span><span class="sxs-lookup"><span data-stu-id="1bb4e-301">Migrate the layout file</span></span>

* <span data-ttu-id="1bb4e-302">Скопируйте файл *_ViewStart. cshtml* из каталога *views* проекта ASP.NET MVC в каталог *представлений* проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="1bb4e-303">Файл *_ViewStart. cshtml* не был изменен в ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="1bb4e-304">Создайте *Общие папки и представления* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="1bb4e-305">*Необязательно:* Скопируйте *_ViewImports. cshtml* из каталога *views* проекта *фулласпнеткоре* MVC в каталог *представлений* проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="1bb4e-306">Удалите любое объявление пространства имен в файле *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="1bb4e-307">Файл *_ViewImports. cshtml* предоставляет пространства имен для всех файлов представлений и переводит их в [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="1bb4e-308">Вспомогательные функции тегов используются в новом файле макета.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="1bb4e-309">Файл *_ViewImports. cshtml* является новым для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="1bb4e-310">Скопируйте файл *_layout. cshtml* из *представлений и общих* каталогов проекта MVC ASP.NET в *представления и общий* каталог проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="1bb4e-311">Откройте файл *_layout. cshtml* и внесите следующие изменения (завершенный код показан ниже):</span><span class="sxs-lookup"><span data-stu-id="1bb4e-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="1bb4e-312">Замените на `@Styles.Render("~/Content/css")` `<link>` элемент для загрузки *начальной загрузки. CSS* (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="1bb4e-313">Удалите `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="1bb4e-314">Закомментируйте `@Html.Partial("_LoginPartial")` строку (заключите строку в `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="1bb4e-315">Дополнительные сведения см. [в разделе Миграция проверки подлинности и Identity в ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="1bb4e-315">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="1bb4e-316">Замените на `@Scripts.Render("~/bundles/jquery")` `<script>` элемент (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="1bb4e-317">Замените на `@Scripts.Render("~/bundles/bootstrap")` `<script>` элемент (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="1bb4e-318">Заменяющая разметка для включения начальной загрузки CSS:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="1bb4e-319">Заменяющая разметка для jQuery и начальная загрузка JavaScript:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="1bb4e-320">Обновленный файл *_layout. cshtml* показан ниже:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="1bb4e-321">Просмотр сайта в браузере.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-321">View the site in the browser.</span></span> <span data-ttu-id="1bb4e-322">Теперь она должна быть правильно загружена с ожидаемыми стилями.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="1bb4e-323">*Необязательно:* Попробуйте использовать новый файл макета.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="1bb4e-324">Скопируйте файл макета из проекта *фулласпнеткоре* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="1bb4e-325">Новый файл макета использует [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и содержит другие улучшения.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="1bb4e-326">Настройка объединения и минификации</span><span class="sxs-lookup"><span data-stu-id="1bb4e-326">Configure bundling and minification</span></span>

<span data-ttu-id="1bb4e-327">Сведения о настройке объединения и минификации см. в разделе [объединение и минификации](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="1bb4e-328">Устранение ошибок HTTP 500</span><span class="sxs-lookup"><span data-stu-id="1bb4e-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="1bb4e-329">Существует множество проблем, которые могут вызвать сообщения об ошибках HTTP 500, не содержащие сведений о источнике проблемы.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="1bb4e-330">Например, если файл *views/_ViewImports. cshtml* содержит пространство имен, которое не существует в проекте, создается ошибка HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="1bb4e-331">По умолчанию в ASP.NET Core приложениях `UseDeveloperExceptionPage` расширение добавляется в `IApplicationBuilder` и выполняется при *разработке*конфигурации.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="1bb4e-332">См. пример в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="1bb4e-333">ASP.NET Core преобразует необработанные исключения в отклики об ошибках HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="1bb4e-334">Как правило, сведения об ошибке не включаются в эти ответы, чтобы предотвратить раскрытие потенциально конфиденциальной информации о сервере.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="1bb4e-335">Дополнительные сведения см. в разделе [Страница "исключение разработчика](xref:fundamentals/error-handling#developer-exception-page)".</span><span class="sxs-lookup"><span data-stu-id="1bb4e-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1bb4e-336">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1bb4e-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="1bb4e-337">В этой статье показано, как начать перенос проекта MVC ASP.NET в [ASP.NET Core MVC](xref:mvc/overview) 2,1.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="1bb4e-338">В процессе он выделяет многие вещи, которые изменились с ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="1bb4e-339">Миграция с ASP.NET MVC — это многоэтапный процесс.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="1bb4e-340">В этой статье рассматриваются следующие вопросы:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-340">This article covers:</span></span>

* <span data-ttu-id="1bb4e-341">Начальная настройка</span><span class="sxs-lookup"><span data-stu-id="1bb4e-341">Initial setup</span></span>
* <span data-ttu-id="1bb4e-342">Основные контроллеры и представления</span><span class="sxs-lookup"><span data-stu-id="1bb4e-342">Basic controllers and views</span></span>
* <span data-ttu-id="1bb4e-343">Статическое содержимое</span><span class="sxs-lookup"><span data-stu-id="1bb4e-343">Static content</span></span>
* <span data-ttu-id="1bb4e-344">Зависимости на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-344">Client-side dependencies.</span></span>

<span data-ttu-id="1bb4e-345">Сведения о переносе конфигурации и Identity кода см. в разделе [Миграция конфигурации в ASP.NET Core](xref:migration/configuration) и [Миграция проверки подлинности и Identity ASP.NET Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-345">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="1bb4e-346">Номера версий в примерах могут быть неактуальными. Обновите проекты соответствующим образом.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="1bb4e-347">Создание проекта MVC для начального ASP.NET</span><span class="sxs-lookup"><span data-stu-id="1bb4e-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="1bb4e-348">Чтобы продемонстрировать обновление, мы начнем с создания приложения ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="1bb4e-349">Создайте его с именем "имя *APP1* ", чтобы пространство имен совпадало с ASP.NET Core проектом, созданным на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Диалоговое окно «Создание проекта» Visual Studio](mvc/_static/new-project.png)

![Диалоговое окно "новое веб-приложение": шаблон проекта MVC, выбранный на панели шаблоны ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="1bb4e-352">*Необязательно:* Измените имя решения с *Mvc5* *на.*</span><span class="sxs-lookup"><span data-stu-id="1bb4e-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="1bb4e-353">Visual Studio отображает новое имя решения (*Mvc5*), которое упрощает указание этого проекта из следующего проекта.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-353">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="1bb4e-354">Создание проекта ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1bb4e-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="1bb4e-355">Создайте *пустое* веб-приложение ASP.NET Core с тем же именем, что и у предыдущего проекта (веб-приложения*APP1*), чтобы пространства имен в двух проектах совпадали.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="1bb4e-356">Наличие одного и того же пространства имен упрощает копирование кода между двумя проектами.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="1bb4e-357">Создайте этот проект в каталоге, отличном от каталога предыдущего проекта, для использования того же имени.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![Диалоговое окно "Новый проект"](mvc/_static/new_core.png)

![Диалоговое окно "Создание веб-приложения ASP.NET": пустой шаблон проекта, выбранный на панели "шаблоны ASP.NET Core"](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="1bb4e-360">*Необязательно:* Создайте новое приложение ASP.NET Core с помощью шаблона проекта *веб-приложения* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="1bb4e-361">Назовите проект *"* имя_проекта" и выберите параметр проверки подлинности для **отдельных учетных записей пользователей**.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-361">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="1bb4e-362">Переименуйте это приложение в *фулласпнеткоре*.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-362">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="1bb4e-363">Создание этого проекта экономит время в преобразовании.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="1bb4e-364">Конечный результат можно просмотреть в коде, созданном шаблоном. код можно скопировать в проект преобразования или сравнить с созданным шаблоном проектом.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="1bb4e-365">Настройка сайта для использования MVC</span><span class="sxs-lookup"><span data-stu-id="1bb4e-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="1bb4e-366">При разработке для .NET Core ссылка на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) используется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="1bb4e-367">Этот пакет содержит пакеты, часто используемые приложениями MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="1bb4e-368">Если целевой объект .NET Framework, ссылки на пакеты должны быть перечислены по отдельности в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="1bb4e-369">`Microsoft.AspNetCore.Mvc` является ASP.NET Core платформой MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="1bb4e-370">`Microsoft.AspNetCore.StaticFiles` является обработчиком статических файлов.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="1bb4e-371">ASP.NET Core приложения явно хотят использовать по промежуточного слоя, например для обслуживания статических файлов.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="1bb4e-372">Дополнительные сведения см. в разделе [Статические файлы](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="1bb4e-373">Откройте файл *Startup.CS* и измените код в соответствии со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="1bb4e-374"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Метод расширения добавляет обработчик статических файлов.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="1bb4e-375">`UseMvc`Метод расширения добавляет маршрутизацию.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="1bb4e-376">Дополнительные сведения см. в разделе Запуск и [Маршрутизация](xref:fundamentals/routing) [приложений](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="1bb4e-377">Добавление контроллера и представления</span><span class="sxs-lookup"><span data-stu-id="1bb4e-377">Add a controller and view</span></span>

<span data-ttu-id="1bb4e-378">В этом разделе добавлены минимальный контроллер и представление, которые будут использоваться в качестве заполнителей для контроллера MVC ASP.NET и представлений, перенесенных в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="1bb4e-379">Добавьте каталог *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="1bb4e-380">Добавьте **класс контроллера** с именем *HomeController.CS* в каталог *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Диалоговое окно ''Добавление нового элемента''](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="1bb4e-382">Добавьте каталог *views* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="1bb4e-383">Добавление *представлений и домашнего* каталога.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="1bb4e-384">Добавьте \*\* Razor представление\*\* с именем *index. cshtml* в каталог *Views/Home* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-384">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Диалоговое окно ''Добавление нового элемента''](mvc/_static/view.png)

<span data-ttu-id="1bb4e-386">Структура проекта показана ниже:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-386">The project structure is shown below:</span></span>

![обозреватель решений отображения файлов и каталогов для файла App1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="1bb4e-388">Замените содержимое файла *Views/Home/Index.cshtml* следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="1bb4e-389">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-389">Run the app.</span></span>

![Веб-приложение открыто в Microsoft ребро](mvc/_static/hello-world.png)

<span data-ttu-id="1bb4e-391">Дополнительные сведения см. в разделе [Controllers](xref:mvc/controllers/actions) and [views](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="1bb4e-392">Следующие функции требуют миграции из примера проекта MVC ASP.NET в проект ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="1bb4e-393">содержимое на стороне клиента (CSS, шрифты и скрипты)</span><span class="sxs-lookup"><span data-stu-id="1bb4e-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="1bb4e-394">controllers</span><span class="sxs-lookup"><span data-stu-id="1bb4e-394">controllers</span></span>

* <span data-ttu-id="1bb4e-395">узел "Представления"</span><span class="sxs-lookup"><span data-stu-id="1bb4e-395">views</span></span>

* <span data-ttu-id="1bb4e-396">модели</span><span class="sxs-lookup"><span data-stu-id="1bb4e-396">models</span></span>

* <span data-ttu-id="1bb4e-397">объединения</span><span class="sxs-lookup"><span data-stu-id="1bb4e-397">bundling</span></span>

* <span data-ttu-id="1bb4e-398">filters</span><span class="sxs-lookup"><span data-stu-id="1bb4e-398">filters</span></span>

* <span data-ttu-id="1bb4e-399">Вход в систему Identity (это делается в следующем руководстве).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-399">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="1bb4e-400">Контроллеры и представления</span><span class="sxs-lookup"><span data-stu-id="1bb4e-400">Controllers and views</span></span>

* <span data-ttu-id="1bb4e-401">Скопируйте каждый из методов из MVC ASP.NET `HomeController` в новый `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="1bb4e-402">В ASP.NET MVC тип возвращаемого значения метода действия контроллера встроенного шаблона — <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; в ASP.NET Core MVC вместо этого методы действия возвращают `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-402">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="1bb4e-403">`ActionResult` реализует `IActionResult` , поэтому нет необходимости изменять тип возвращаемого значения методов действия.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="1bb4e-404">Скопируйте файлы представления *About. cshtml*, *Contact. cshtml*и *index. cshtml* Razor из проекта MVC ASP.NET в проект ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-404">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="1bb4e-405">Тестирование каждого метода</span><span class="sxs-lookup"><span data-stu-id="1bb4e-405">Test each method</span></span>

<span data-ttu-id="1bb4e-406">Файл макета и стили еще не перенесены, поэтому отображаемые представления содержат только содержимое в файлах представления.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="1bb4e-407">Файл макета, в котором созданы ссылки `About` для `Contact` представлений и, пока не будет доступен.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="1bb4e-408">Вызовите готовые представления из браузера в запущенном приложении ASP.NET Core, заменив текущий номер порта на номер порта, используемый в проекте ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="1bb4e-409">Например: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-409">For example: `https://localhost:44375/home/about`.</span></span>

![Страница контактов](mvc/_static/contact-page.png)

<span data-ttu-id="1bb4e-411">Обратите внимание на отсутствие стилей и пунктов меню.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="1bb4e-412">Стиль будет исправлен в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="1bb4e-413">Статическое содержимое</span><span class="sxs-lookup"><span data-stu-id="1bb4e-413">Static content</span></span>

<span data-ttu-id="1bb4e-414">В ASP.NET MVC 5 и более ранних версиях статическое содержимое было размещено из корневого каталога веб-проекта и было смешано с файлами на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="1bb4e-415">В ASP.NET Core статическое содержимое размещается в каталоге *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="1bb4e-416">Скопируйте статическое содержимое из приложения ASP.NET MVC в каталог *wwwroot* в проекте ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="1bb4e-417">В этом примере преобразования:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-417">In this sample conversion:</span></span>

* <span data-ttu-id="1bb4e-418">Скопируйте файл *фавикон. ico* из проекта MVC ASP.NET в каталог *wwwroot* в проекте ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="1bb4e-419">Проект ASP.NET MVC использует для своей стилизации [начальную загрузку](https://getbootstrap.com/) и сохраняет файлы начальной загрузки в каталогах *содержимого* и *скриптов* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="1bb4e-420">Шаблон, создавший проект MVC ASP.NET, ссылается на загрузку в файле макета (*views/Shared/_layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="1bb4e-421">Файлы *bootstrap.js* и *начальной загрузки CSS* можно скопировать из проекта MVC ASP.NET в каталог *wwwroot* в новом проекте.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="1bb4e-422">Вместо этого в этом документе добавляется поддержка начальной загрузки (и других клиентских библиотек) с помощью CDN в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="1bb4e-423">Перенос файла макета</span><span class="sxs-lookup"><span data-stu-id="1bb4e-423">Migrate the layout file</span></span>

* <span data-ttu-id="1bb4e-424">Скопируйте файл *_ViewStart. cshtml* из каталога *views* проекта ASP.NET MVC в каталог *представлений* проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="1bb4e-425">Файл *_ViewStart. cshtml* не был изменен в ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="1bb4e-426">Создайте *Общие папки и представления* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="1bb4e-427">*Необязательно:* Скопируйте *_ViewImports. cshtml* из каталога *views* проекта *фулласпнеткоре* MVC в каталог *представлений* проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="1bb4e-428">Удалите любое объявление пространства имен в файле *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="1bb4e-429">Файл *_ViewImports. cshtml* предоставляет пространства имен для всех файлов представлений и переводит их в [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="1bb4e-430">Вспомогательные функции тегов используются в новом файле макета.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="1bb4e-431">Файл *_ViewImports. cshtml* является новым для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="1bb4e-432">Скопируйте файл *_layout. cshtml* из *представлений и общих* каталогов проекта MVC ASP.NET в *представления и общий* каталог проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="1bb4e-433">Откройте файл *_layout. cshtml* и внесите следующие изменения (завершенный код показан ниже):</span><span class="sxs-lookup"><span data-stu-id="1bb4e-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="1bb4e-434">Замените на `@Styles.Render("~/Content/css")` `<link>` элемент для загрузки *начальной загрузки. CSS* (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="1bb4e-435">Удалите `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="1bb4e-436">Закомментируйте `@Html.Partial("_LoginPartial")` строку (заключите строку в `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="1bb4e-437">Дополнительные сведения см. [в разделе Миграция проверки подлинности и Identity в ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="1bb4e-437">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="1bb4e-438">Замените на `@Scripts.Render("~/bundles/jquery")` `<script>` элемент (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="1bb4e-439">Замените на `@Scripts.Render("~/bundles/bootstrap")` `<script>` элемент (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="1bb4e-440">Заменяющая разметка для включения начальной загрузки CSS:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="1bb4e-441">Заменяющая разметка для jQuery и начальная загрузка JavaScript:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="1bb4e-442">Обновленный файл *_layout. cshtml* показан ниже:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="1bb4e-443">Просмотр сайта в браузере.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-443">View the site in the browser.</span></span> <span data-ttu-id="1bb4e-444">Теперь она должна быть правильно загружена с ожидаемыми стилями.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="1bb4e-445">*Необязательно:* Попробуйте использовать новый файл макета.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="1bb4e-446">Скопируйте файл макета из проекта *фулласпнеткоре* .</span><span class="sxs-lookup"><span data-stu-id="1bb4e-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="1bb4e-447">Новый файл макета использует [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и содержит другие улучшения.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="1bb4e-448">Настройка объединения и минификации</span><span class="sxs-lookup"><span data-stu-id="1bb4e-448">Configure bundling and minification</span></span>

<span data-ttu-id="1bb4e-449">Сведения о настройке объединения и минификации см. в разделе [объединение и минификации](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="1bb4e-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="1bb4e-450">Устранение ошибок HTTP 500</span><span class="sxs-lookup"><span data-stu-id="1bb4e-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="1bb4e-451">Существует множество проблем, которые могут вызвать сообщения об ошибках HTTP 500, не содержащие сведений о источнике проблемы.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="1bb4e-452">Например, если файл *views/_ViewImports. cshtml* содержит пространство имен, которое не существует в проекте, создается ошибка HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="1bb4e-453">По умолчанию в ASP.NET Core приложениях `UseDeveloperExceptionPage` расширение добавляется в `IApplicationBuilder` и выполняется при *разработке*конфигурации.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="1bb4e-454">См. пример в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="1bb4e-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="1bb4e-455">ASP.NET Core преобразует необработанные исключения в отклики об ошибках HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="1bb4e-456">Как правило, сведения об ошибке не включаются в эти ответы, чтобы предотвратить раскрытие потенциально конфиденциальной информации о сервере.</span><span class="sxs-lookup"><span data-stu-id="1bb4e-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="1bb4e-457">Дополнительные сведения см. в разделе [Страница "исключение разработчика](xref:fundamentals/error-handling#developer-exception-page)".</span><span class="sxs-lookup"><span data-stu-id="1bb4e-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1bb4e-458">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1bb4e-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
