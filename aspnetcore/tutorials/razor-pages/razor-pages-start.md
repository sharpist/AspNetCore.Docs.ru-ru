---
title: Учебник. Начало работы с Razor Pages в ASP.NET Core
author: rick-anderson
description: В этой серии учебников объясняется, как использовать Razor Pages в ASP.NET Core. Узнайте, как создать модель, сгенерировать код для Razor Pages, использовать Entity Framework Core и SQL Server для доступа к данным, добавлять функции поиска и проверки ввода, а также использовать возможность миграции для обновления модели.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 5fb841de2fa9a04cf05aaf08f255041ee1952638
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021098"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="0b320-104">Учебник. Начало работы с Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0b320-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="0b320-105">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="0b320-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="0b320-106">В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0b320-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="0b320-107">Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="0b320-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="0b320-108">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="0b320-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0b320-109">Создание веб-приложения Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0b320-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="0b320-110">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="0b320-110">Run the app.</span></span>
> * <span data-ttu-id="0b320-111">Анализ файлов проекта.</span><span class="sxs-lookup"><span data-stu-id="0b320-111">Examine the project files.</span></span>

<span data-ttu-id="0b320-112">Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.</span><span class="sxs-lookup"><span data-stu-id="0b320-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="0b320-114">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="0b320-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b320-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b320-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b320-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b320-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b320-117">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="0b320-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="0b320-118">Создание веб-приложения Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0b320-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b320-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b320-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b320-120">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="0b320-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0b320-121">Создайте веб-приложение ASP.NET Core и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="0b320-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="0b320-122">![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="0b320-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="0b320-123">Присвойте проекту имя **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="0b320-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="0b320-124">Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.</span><span class="sxs-lookup"><span data-stu-id="0b320-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="0b320-125">![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="0b320-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="0b320-126">Выберите в раскрывающемся списке пункт **ASP.NET Core 3.1**, затем — **Веб-приложение** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="0b320-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="0b320-128">Создается следующий начальный проект:</span><span class="sxs-lookup"><span data-stu-id="0b320-128">The following starter project is created:</span></span>

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b320-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b320-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0b320-131">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0b320-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="0b320-132">Перейдите в каталог `cd`, в котором находится проект.</span><span class="sxs-lookup"><span data-stu-id="0b320-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="0b320-133">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="0b320-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="0b320-134">Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="0b320-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="0b320-135">Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0b320-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="0b320-136">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="0b320-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="0b320-137">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="0b320-137">Select **Yes**.</span></span>

  <span data-ttu-id="0b320-138">Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.</span><span class="sxs-lookup"><span data-stu-id="0b320-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b320-139">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="0b320-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0b320-140">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="0b320-140">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="0b320-142">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="0b320-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="0b320-143">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="0b320-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Выбор шаблона веб-приложения macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="0b320-145">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="0b320-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="0b320-146">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="0b320-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="0b320-147">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 3.x.</span><span class="sxs-lookup"><span data-stu-id="0b320-147">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="0b320-148">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="0b320-148">Select **Next**.</span></span>

* <span data-ttu-id="0b320-149">Присвойте проекту имя **RazorPagesMovie** и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="0b320-149">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![Имя проекта macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="0b320-151">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="0b320-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="0b320-152">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="0b320-152">Examine the project files</span></span>

<span data-ttu-id="0b320-153">Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.</span><span class="sxs-lookup"><span data-stu-id="0b320-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="0b320-154">Папка Pages</span><span class="sxs-lookup"><span data-stu-id="0b320-154">Pages folder</span></span>

<span data-ttu-id="0b320-155">Содержит страницы Razor и вспомогательные файлы.</span><span class="sxs-lookup"><span data-stu-id="0b320-155">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="0b320-156">Каждая страница Razor — это пара файлов.</span><span class="sxs-lookup"><span data-stu-id="0b320-156">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="0b320-157">Файл *CSHTML*, содержащий HTML-разметку с кодом C# и синтаксисом Razor.</span><span class="sxs-lookup"><span data-stu-id="0b320-157">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="0b320-158">Файл *. cshtml.cs*, содержащий код C#, который обрабатывает события страницы.</span><span class="sxs-lookup"><span data-stu-id="0b320-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="0b320-159">Имена вспомогательных файлов начинаются с символа подчеркивания.</span><span class="sxs-lookup"><span data-stu-id="0b320-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="0b320-160">Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц.</span><span class="sxs-lookup"><span data-stu-id="0b320-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="0b320-161">Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="0b320-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="0b320-162">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="0b320-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="0b320-163">Папка wwwroot</span><span class="sxs-lookup"><span data-stu-id="0b320-163">wwwroot folder</span></span>

<span data-ttu-id="0b320-164">Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы.</span><span class="sxs-lookup"><span data-stu-id="0b320-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="0b320-165">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="0b320-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="0b320-166">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="0b320-166">appSettings.json</span></span>

<span data-ttu-id="0b320-167">Содержит данные конфигурации, например строки подключения.</span><span class="sxs-lookup"><span data-stu-id="0b320-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="0b320-168">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0b320-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="0b320-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="0b320-169">Program.cs</span></span>

<span data-ttu-id="0b320-170">Содержит точку входа для программы.</span><span class="sxs-lookup"><span data-stu-id="0b320-170">Contains the entry point for the program.</span></span> <span data-ttu-id="0b320-171">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="0b320-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="0b320-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0b320-172">Startup.cs</span></span>

<span data-ttu-id="0b320-173">содержит код, задающий поведение приложения.</span><span class="sxs-lookup"><span data-stu-id="0b320-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="0b320-174">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0b320-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0b320-175">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="0b320-175">Next steps</span></span>

<span data-ttu-id="0b320-176">Перейдите к следующему учебнику в серии:</span><span class="sxs-lookup"><span data-stu-id="0b320-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0b320-177">Добавление модели</span><span class="sxs-lookup"><span data-stu-id="0b320-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0b320-178">Это первый учебник из серии.</span><span class="sxs-lookup"><span data-stu-id="0b320-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="0b320-179">В этой [серии](xref:tutorials/razor-pages/index) приводятся основные сведения о сборке веб-приложения Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0b320-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="0b320-180">Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="0b320-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="0b320-181">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="0b320-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0b320-182">Создание веб-приложения Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0b320-182">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="0b320-183">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="0b320-183">Run the app.</span></span>
> * <span data-ttu-id="0b320-184">Анализ файлов проекта.</span><span class="sxs-lookup"><span data-stu-id="0b320-184">Examine the project files.</span></span>

<span data-ttu-id="0b320-185">Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.</span><span class="sxs-lookup"><span data-stu-id="0b320-185">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="0b320-187">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="0b320-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b320-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b320-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b320-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b320-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b320-190">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="0b320-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="0b320-191">Создание веб-приложения Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0b320-191">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b320-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b320-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b320-193">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="0b320-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="0b320-194">Создайте веб-приложение ASP.NET Core и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="0b320-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="0b320-196">Присвойте проекту имя **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="0b320-196">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="0b320-197">Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.</span><span class="sxs-lookup"><span data-stu-id="0b320-197">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="0b320-199">Выберите в раскрывающемся списке пункт **ASP.NET Core 2.2**, затем — **Веб-приложение** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="0b320-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="0b320-201">Создается следующий начальный проект:</span><span class="sxs-lookup"><span data-stu-id="0b320-201">The following starter project is created:</span></span>

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b320-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b320-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0b320-204">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0b320-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="0b320-205">Перейдите в каталог `cd`, в котором находится проект.</span><span class="sxs-lookup"><span data-stu-id="0b320-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="0b320-206">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="0b320-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="0b320-207">Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="0b320-207">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="0b320-208">Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0b320-208">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="0b320-209">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="0b320-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="0b320-210">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="0b320-210">Select **Yes**.</span></span>

  <span data-ttu-id="0b320-211">Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.</span><span class="sxs-lookup"><span data-stu-id="0b320-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b320-212">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="0b320-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0b320-213">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="0b320-213">Select **File** > **New Solution**.</span></span>

![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="0b320-215">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="0b320-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="0b320-216">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="0b320-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="0b320-217">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="0b320-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="0b320-218">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="0b320-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="0b320-219">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 2.x.</span><span class="sxs-lookup"><span data-stu-id="0b320-219">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="0b320-220">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="0b320-220">Select **Next**.</span></span>

* <span data-ttu-id="0b320-221">Присвойте проекту имя **RazorPagesMovie** и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="0b320-221">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="0b320-223">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="0b320-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b320-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b320-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b320-225">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="0b320-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="0b320-226">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="0b320-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="0b320-227">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0b320-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0b320-228">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="0b320-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="0b320-229">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="0b320-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="0b320-230">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="0b320-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="0b320-231">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="0b320-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="0b320-232">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="0b320-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="0b320-234">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="0b320-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="0b320-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b320-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="0b320-237">Нажмите клавиши **CTRL-F5**, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="0b320-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="0b320-238">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="0b320-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="0b320-239">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0b320-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0b320-240">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="0b320-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="0b320-241">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="0b320-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="0b320-242">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="0b320-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="0b320-243">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="0b320-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="0b320-245">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="0b320-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b320-247">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="0b320-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="0b320-248">Нажмите клавиши **Cmd-Opt-F5**, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="0b320-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="0b320-249">Visual Studio запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="0b320-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="0b320-250">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="0b320-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="0b320-251">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="0b320-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="0b320-253">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="0b320-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="0b320-255">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="0b320-255">Examine the project files</span></span>

<span data-ttu-id="0b320-256">Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.</span><span class="sxs-lookup"><span data-stu-id="0b320-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="0b320-257">Папка Pages</span><span class="sxs-lookup"><span data-stu-id="0b320-257">Pages folder</span></span>

<span data-ttu-id="0b320-258">Содержит страницы Razor и вспомогательные файлы.</span><span class="sxs-lookup"><span data-stu-id="0b320-258">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="0b320-259">Каждая страница Razor — это пара файлов.</span><span class="sxs-lookup"><span data-stu-id="0b320-259">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="0b320-260">Файл *CSHTML*, содержащий HTML-разметку с кодом C# и синтаксисом Razor.</span><span class="sxs-lookup"><span data-stu-id="0b320-260">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="0b320-261">Файл *. cshtml.cs*, содержащий код C#, который обрабатывает события страницы.</span><span class="sxs-lookup"><span data-stu-id="0b320-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="0b320-262">Имена вспомогательных файлов начинаются с символа подчеркивания.</span><span class="sxs-lookup"><span data-stu-id="0b320-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="0b320-263">Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц.</span><span class="sxs-lookup"><span data-stu-id="0b320-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="0b320-264">Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="0b320-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="0b320-265">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="0b320-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="0b320-266">Папка wwwroot</span><span class="sxs-lookup"><span data-stu-id="0b320-266">wwwroot folder</span></span>

<span data-ttu-id="0b320-267">Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы.</span><span class="sxs-lookup"><span data-stu-id="0b320-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="0b320-268">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="0b320-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="0b320-269">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="0b320-269">appSettings.json</span></span>

<span data-ttu-id="0b320-270">Содержит данные конфигурации, например строки подключения.</span><span class="sxs-lookup"><span data-stu-id="0b320-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="0b320-271">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0b320-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="0b320-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="0b320-272">Program.cs</span></span>

<span data-ttu-id="0b320-273">Содержит точку входа для программы.</span><span class="sxs-lookup"><span data-stu-id="0b320-273">Contains the entry point for the program.</span></span> <span data-ttu-id="0b320-274">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="0b320-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="0b320-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0b320-275">Startup.cs</span></span>

<span data-ttu-id="0b320-276">Содержит код, который настраивает поведение приложения, например, требуется ли согласие для файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="0b320-276">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="0b320-277">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0b320-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0b320-278">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="0b320-278">Additional resources</span></span>

* [<span data-ttu-id="0b320-279">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="0b320-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="0b320-280">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="0b320-280">Next steps</span></span>

<span data-ttu-id="0b320-281">Перейдите к следующему учебнику в серии:</span><span class="sxs-lookup"><span data-stu-id="0b320-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0b320-282">Добавление модели</span><span class="sxs-lookup"><span data-stu-id="0b320-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
