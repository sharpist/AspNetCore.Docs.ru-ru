---
title: Учебник. Начало работы с Razor Pages в ASP.NET Core
author: rick-anderson
description: В этой серии учебников объясняется, как использовать Razor Pages в ASP.NET Core. Узнайте, как создать модель, сгенерировать код для Razor Pages, использовать Entity Framework Core и SQL Server для доступа к данным, добавлять функции поиска и проверки ввода, а также использовать возможность миграции для обновления модели.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 3b8ccf639bb91234f81c67750fffa170e52d636f
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452356"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="de28f-104">Учебник. Начало работы с Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="de28f-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="de28f-105">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="de28f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="de28f-106">В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="de28f-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="de28f-107">Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="de28f-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="de28f-108">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="de28f-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="de28f-109">Создание веб-приложения Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="de28f-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="de28f-110">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="de28f-110">Run the app.</span></span>
> * <span data-ttu-id="de28f-111">Анализ файлов проекта.</span><span class="sxs-lookup"><span data-stu-id="de28f-111">Examine the project files.</span></span>

<span data-ttu-id="de28f-112">Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.</span><span class="sxs-lookup"><span data-stu-id="de28f-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="de28f-114">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="de28f-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de28f-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de28f-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="de28f-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de28f-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de28f-117">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="de28f-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="de28f-118">Создание веб-приложения Razor Pages</span><span class="sxs-lookup"><span data-stu-id="de28f-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de28f-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de28f-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="de28f-120">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="de28f-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="de28f-121">Создайте веб-приложение ASP.NET Core и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="de28f-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="de28f-122">![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="de28f-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="de28f-123">Назовите проект **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="de28f-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="de28f-124">Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.</span><span class="sxs-lookup"><span data-stu-id="de28f-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="de28f-125">![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="de28f-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="de28f-126">Выберите в раскрывающемся списке пункт **ASP.NET Core 3.1**, затем — **Веб-приложение** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="de28f-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="de28f-128">Создается следующий начальный проект:</span><span class="sxs-lookup"><span data-stu-id="de28f-128">The following starter project is created:</span></span>

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="de28f-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de28f-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="de28f-131">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="de28f-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="de28f-132">Перейдите в каталог `cd`, в котором находится проект.</span><span class="sxs-lookup"><span data-stu-id="de28f-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="de28f-133">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="de28f-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="de28f-134">Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="de28f-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="de28f-135">Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="de28f-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="de28f-136">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **Required assets to build and debug are missing from 'RazorPagesMovie'. (В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки.) Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="de28f-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="de28f-137">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="de28f-137">Select **Yes**.</span></span>

  <span data-ttu-id="de28f-138">Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.</span><span class="sxs-lookup"><span data-stu-id="de28f-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de28f-139">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="de28f-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="de28f-140">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="de28f-140">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="de28f-142">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="de28f-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="de28f-143">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="de28f-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Выбор шаблона веб-приложения macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="de28f-145">Подтвердите следующие конфигурации.</span><span class="sxs-lookup"><span data-stu-id="de28f-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="de28f-146">Для параметра **Целевая платформа** задано значение **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="de28f-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="de28f-147">Для параметра **Проверка подлинности** задано значение **Без проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="de28f-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="de28f-148">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="de28f-148">Select **Next**.</span></span>

  ![Выбор .NET Core 3.1 для macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="de28f-150">Присвойте проекту имя **RazorPagesMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="de28f-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![Имя проекта macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="de28f-152">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="de28f-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="de28f-153">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="de28f-153">Examine the project files</span></span>

<span data-ttu-id="de28f-154">Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.</span><span class="sxs-lookup"><span data-stu-id="de28f-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="de28f-155">Папка Pages</span><span class="sxs-lookup"><span data-stu-id="de28f-155">Pages folder</span></span>

<span data-ttu-id="de28f-156">Содержит страницы Razor и вспомогательные файлы.</span><span class="sxs-lookup"><span data-stu-id="de28f-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="de28f-157">Каждая страница Razor — это пара файлов.</span><span class="sxs-lookup"><span data-stu-id="de28f-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="de28f-158">Файл *CSHTML*, содержащий HTML-разметку с кодом C# и синтаксисом Razor.</span><span class="sxs-lookup"><span data-stu-id="de28f-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="de28f-159">Файл *. cshtml.cs*, содержащий код C#, который обрабатывает события страницы.</span><span class="sxs-lookup"><span data-stu-id="de28f-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="de28f-160">Имена вспомогательных файлов начинаются с символа подчеркивания.</span><span class="sxs-lookup"><span data-stu-id="de28f-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="de28f-161">Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц.</span><span class="sxs-lookup"><span data-stu-id="de28f-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="de28f-162">Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="de28f-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="de28f-163">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="de28f-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="de28f-164">Папка wwwroot</span><span class="sxs-lookup"><span data-stu-id="de28f-164">wwwroot folder</span></span>

<span data-ttu-id="de28f-165">Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы.</span><span class="sxs-lookup"><span data-stu-id="de28f-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="de28f-166">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="de28f-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="de28f-167">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="de28f-167">appSettings.json</span></span>

<span data-ttu-id="de28f-168">Содержит данные конфигурации, например строки подключения.</span><span class="sxs-lookup"><span data-stu-id="de28f-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="de28f-169">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="de28f-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="de28f-170">Program.cs</span><span class="sxs-lookup"><span data-stu-id="de28f-170">Program.cs</span></span>

<span data-ttu-id="de28f-171">Содержит точку входа для программы.</span><span class="sxs-lookup"><span data-stu-id="de28f-171">Contains the entry point for the program.</span></span> <span data-ttu-id="de28f-172">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="de28f-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="de28f-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="de28f-173">Startup.cs</span></span>

<span data-ttu-id="de28f-174">содержит код, задающий поведение приложения.</span><span class="sxs-lookup"><span data-stu-id="de28f-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="de28f-175">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="de28f-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="de28f-176">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="de28f-176">Next steps</span></span>

<span data-ttu-id="de28f-177">Перейдите к следующему учебнику в серии:</span><span class="sxs-lookup"><span data-stu-id="de28f-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="de28f-178">Добавление модели</span><span class="sxs-lookup"><span data-stu-id="de28f-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="de28f-179">Это первый учебник из серии.</span><span class="sxs-lookup"><span data-stu-id="de28f-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="de28f-180">В этой [серии](xref:tutorials/razor-pages/index) приводятся основные сведения о сборке веб-приложения Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="de28f-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="de28f-181">Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="de28f-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="de28f-182">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="de28f-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="de28f-183">Создание веб-приложения Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="de28f-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="de28f-184">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="de28f-184">Run the app.</span></span>
> * <span data-ttu-id="de28f-185">Анализ файлов проекта.</span><span class="sxs-lookup"><span data-stu-id="de28f-185">Examine the project files.</span></span>

<span data-ttu-id="de28f-186">Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.</span><span class="sxs-lookup"><span data-stu-id="de28f-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="de28f-188">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="de28f-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de28f-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de28f-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="de28f-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de28f-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de28f-191">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="de28f-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="de28f-192">Создание веб-приложения Razor Pages</span><span class="sxs-lookup"><span data-stu-id="de28f-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de28f-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de28f-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="de28f-194">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="de28f-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="de28f-195">Создайте веб-приложение ASP.NET Core и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="de28f-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="de28f-197">Назовите проект **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="de28f-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="de28f-198">Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.</span><span class="sxs-lookup"><span data-stu-id="de28f-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="de28f-200">Выберите в раскрывающемся списке пункт **ASP.NET Core 2.2**, затем — **Веб-приложение** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="de28f-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="de28f-202">Создается следующий начальный проект:</span><span class="sxs-lookup"><span data-stu-id="de28f-202">The following starter project is created:</span></span>

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="de28f-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de28f-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="de28f-205">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="de28f-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="de28f-206">Перейдите в каталог `cd`, в котором находится проект.</span><span class="sxs-lookup"><span data-stu-id="de28f-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="de28f-207">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="de28f-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="de28f-208">Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="de28f-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="de28f-209">Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="de28f-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="de28f-210">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **Required assets to build and debug are missing from 'RazorPagesMovie'. (В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки.) Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="de28f-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="de28f-211">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="de28f-211">Select **Yes**.</span></span>

  <span data-ttu-id="de28f-212">Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.</span><span class="sxs-lookup"><span data-stu-id="de28f-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de28f-213">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="de28f-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="de28f-214">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="de28f-214">Select **File** > **New Solution**.</span></span>

![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="de28f-216">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="de28f-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="de28f-217">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="de28f-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="de28f-218">В диалоговом окне **Настройка нового веб-API ASP.NET Core** установите для параметра **Целевая платформа** значение **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="de28f-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Выбор .NET Core 3.0 для macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="de28f-220">Присвойте проекту имя **RazorPagesMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="de28f-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="de28f-222">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="de28f-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de28f-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de28f-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="de28f-224">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="de28f-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="de28f-225">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="de28f-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="de28f-226">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="de28f-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="de28f-227">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="de28f-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="de28f-228">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="de28f-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="de28f-229">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="de28f-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="de28f-230">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="de28f-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="de28f-231">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="de28f-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="de28f-233">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="de28f-233">The following image shows the app after you give consent to tracking:</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="de28f-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de28f-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="de28f-236">Нажмите клавиши **CTRL-F5**, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="de28f-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="de28f-237">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="de28f-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="de28f-238">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="de28f-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="de28f-239">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="de28f-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="de28f-240">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="de28f-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="de28f-241">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="de28f-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="de28f-242">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="de28f-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="de28f-244">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="de28f-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de28f-246">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="de28f-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="de28f-247">Нажмите клавиши **Cmd-Opt-F5**, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="de28f-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="de28f-248">Visual Studio запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="de28f-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="de28f-249">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="de28f-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="de28f-250">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="de28f-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="de28f-252">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="de28f-252">The following image shows the app after you give consent to tracking:</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="de28f-254">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="de28f-254">Examine the project files</span></span>

<span data-ttu-id="de28f-255">Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.</span><span class="sxs-lookup"><span data-stu-id="de28f-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="de28f-256">Папка Pages</span><span class="sxs-lookup"><span data-stu-id="de28f-256">Pages folder</span></span>

<span data-ttu-id="de28f-257">Содержит страницы Razor и вспомогательные файлы.</span><span class="sxs-lookup"><span data-stu-id="de28f-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="de28f-258">Каждая страница Razor — это пара файлов.</span><span class="sxs-lookup"><span data-stu-id="de28f-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="de28f-259">Файл *CSHTML*, содержащий HTML-разметку с кодом C# и синтаксисом Razor.</span><span class="sxs-lookup"><span data-stu-id="de28f-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="de28f-260">Файл *. cshtml.cs*, содержащий код C#, который обрабатывает события страницы.</span><span class="sxs-lookup"><span data-stu-id="de28f-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="de28f-261">Имена вспомогательных файлов начинаются с символа подчеркивания.</span><span class="sxs-lookup"><span data-stu-id="de28f-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="de28f-262">Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц.</span><span class="sxs-lookup"><span data-stu-id="de28f-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="de28f-263">Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="de28f-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="de28f-264">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="de28f-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="de28f-265">Папка wwwroot</span><span class="sxs-lookup"><span data-stu-id="de28f-265">wwwroot folder</span></span>

<span data-ttu-id="de28f-266">Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы.</span><span class="sxs-lookup"><span data-stu-id="de28f-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="de28f-267">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="de28f-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="de28f-268">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="de28f-268">appSettings.json</span></span>

<span data-ttu-id="de28f-269">Содержит данные конфигурации, например строки подключения.</span><span class="sxs-lookup"><span data-stu-id="de28f-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="de28f-270">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="de28f-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="de28f-271">Program.cs</span><span class="sxs-lookup"><span data-stu-id="de28f-271">Program.cs</span></span>

<span data-ttu-id="de28f-272">Содержит точку входа для программы.</span><span class="sxs-lookup"><span data-stu-id="de28f-272">Contains the entry point for the program.</span></span> <span data-ttu-id="de28f-273">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="de28f-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="de28f-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="de28f-274">Startup.cs</span></span>

<span data-ttu-id="de28f-275">Содержит код, который настраивает поведение приложения, например, требуется ли согласие для файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="de28f-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="de28f-276">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="de28f-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="de28f-277">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="de28f-277">Additional resources</span></span>

* [<span data-ttu-id="de28f-278">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="de28f-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="de28f-279">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="de28f-279">Next steps</span></span>

<span data-ttu-id="de28f-280">Перейдите к следующему учебнику в серии:</span><span class="sxs-lookup"><span data-stu-id="de28f-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="de28f-281">Добавление модели</span><span class="sxs-lookup"><span data-stu-id="de28f-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
