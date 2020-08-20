---
title: Учебник. Начало работы с Razor Pages в ASP.NET Core
author: rick-anderson
description: В этой серии учебников объясняется, как использовать Razor Pages в ASP.NET Core. Узнайте, как создать модель, сгенерировать код для Razor Pages, использовать Entity Framework Core и SQL Server для доступа к данным, добавлять функции поиска и проверки ввода, а также использовать возможность миграции для обновления модели.
ms.author: riande
ms.date: 11/12/2019
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: db05b8243b91a936a6ed72b0445e0770d44df014
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634583"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="fe8e9-104">Учебник. Начало работы с Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe8e9-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="fe8e9-105">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="fe8e9-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="fe8e9-106">В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="fe8e9-107">Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="fe8e9-108">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe8e9-109">Создание веб-приложения Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="fe8e9-110">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-110">Run the app.</span></span>
> * <span data-ttu-id="fe8e9-111">Анализ файлов проекта.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-111">Examine the project files.</span></span>

<span data-ttu-id="fe8e9-112">Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="fe8e9-114">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="fe8e9-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe8e9-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe8e9-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe8e9-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe8e9-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe8e9-117">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fe8e9-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="fe8e9-118">Создание веб-приложения Razor Pages</span><span class="sxs-lookup"><span data-stu-id="fe8e9-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe8e9-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe8e9-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fe8e9-120">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="fe8e9-121">Создайте веб-приложение ASP.NET Core и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="fe8e9-122">![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="fe8e9-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="fe8e9-123">Присвойте проекту имя **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="fe8e9-124">Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="fe8e9-125">![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="fe8e9-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="fe8e9-126">Выберите в раскрывающемся списке пункт **ASP.NET Core 3.1**, затем — **Веб-приложение** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="fe8e9-128">Создается следующий начальный проект:</span><span class="sxs-lookup"><span data-stu-id="fe8e9-128">The following starter project is created:</span></span>

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe8e9-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe8e9-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fe8e9-131">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fe8e9-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="fe8e9-132">Перейдите в каталог `cd`, в котором находится проект.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="fe8e9-133">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="fe8e9-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="fe8e9-134">Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="fe8e9-135">Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="fe8e9-136">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="fe8e9-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="fe8e9-137">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-137">Select **Yes**.</span></span>

  <span data-ttu-id="fe8e9-138">Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe8e9-139">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fe8e9-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fe8e9-140">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-140">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="fe8e9-142">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="fe8e9-143">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Выбор шаблона веб-приложения macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="fe8e9-145">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="fe8e9-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="fe8e9-146">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="fe8e9-147">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 3.x.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-147">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="fe8e9-148">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-148">Select **Next**.</span></span>

* <span data-ttu-id="fe8e9-149">Присвойте проекту имя **RazorPagesMovie** и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-149">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![Имя проекта macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="fe8e9-151">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="fe8e9-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="fe8e9-152">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="fe8e9-152">Examine the project files</span></span>

<span data-ttu-id="fe8e9-153">Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="fe8e9-154">Папка Pages</span><span class="sxs-lookup"><span data-stu-id="fe8e9-154">Pages folder</span></span>

<span data-ttu-id="fe8e9-155">Содержит страницы Razor и вспомогательные файлы.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-155">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="fe8e9-156">Каждая страница Razor — это пара файлов.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-156">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="fe8e9-157">Файл *CSHTML*, содержащий HTML-разметку с кодом C# и синтаксисом Razor.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-157">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="fe8e9-158">Файл *. cshtml.cs*, содержащий код C#, который обрабатывает события страницы.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="fe8e9-159">Имена вспомогательных файлов начинаются с символа подчеркивания.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="fe8e9-160">Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="fe8e9-161">Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="fe8e9-162">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="fe8e9-163">Папка wwwroot</span><span class="sxs-lookup"><span data-stu-id="fe8e9-163">wwwroot folder</span></span>

<span data-ttu-id="fe8e9-164">Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="fe8e9-165">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="fe8e9-166">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="fe8e9-166">appSettings.json</span></span>

<span data-ttu-id="fe8e9-167">Содержит данные конфигурации, например строки подключения.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="fe8e9-168">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="fe8e9-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="fe8e9-169">Program.cs</span></span>

<span data-ttu-id="fe8e9-170">Содержит точку входа для программы.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-170">Contains the entry point for the program.</span></span> <span data-ttu-id="fe8e9-171">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="fe8e9-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="fe8e9-172">Startup.cs</span></span>

<span data-ttu-id="fe8e9-173">содержит код, задающий поведение приложения.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="fe8e9-174">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fe8e9-175">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="fe8e9-175">Next steps</span></span>

<span data-ttu-id="fe8e9-176">Перейдите к следующему учебнику в серии:</span><span class="sxs-lookup"><span data-stu-id="fe8e9-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="fe8e9-177">Добавление модели</span><span class="sxs-lookup"><span data-stu-id="fe8e9-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fe8e9-178">Это первый учебник из серии.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="fe8e9-179">В этой [серии](xref:tutorials/razor-pages/index) приводятся основные сведения о сборке веб-приложения Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="fe8e9-180">Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="fe8e9-181">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe8e9-182">Создание веб-приложения Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-182">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="fe8e9-183">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-183">Run the app.</span></span>
> * <span data-ttu-id="fe8e9-184">Анализ файлов проекта.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-184">Examine the project files.</span></span>

<span data-ttu-id="fe8e9-185">Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-185">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="fe8e9-187">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="fe8e9-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe8e9-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe8e9-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe8e9-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe8e9-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe8e9-190">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fe8e9-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="fe8e9-191">Создание веб-приложения Razor Pages</span><span class="sxs-lookup"><span data-stu-id="fe8e9-191">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe8e9-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe8e9-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fe8e9-193">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="fe8e9-194">Создайте веб-приложение ASP.NET Core и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="fe8e9-196">Присвойте проекту имя **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-196">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="fe8e9-197">Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-197">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="fe8e9-199">Выберите в раскрывающемся списке пункт **ASP.NET Core 2.2**, затем — **Веб-приложение** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="fe8e9-201">Создается следующий начальный проект:</span><span class="sxs-lookup"><span data-stu-id="fe8e9-201">The following starter project is created:</span></span>

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe8e9-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe8e9-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fe8e9-204">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fe8e9-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="fe8e9-205">Перейдите в каталог `cd`, в котором находится проект.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="fe8e9-206">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="fe8e9-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="fe8e9-207">Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-207">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="fe8e9-208">Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-208">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="fe8e9-209">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="fe8e9-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="fe8e9-210">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-210">Select **Yes**.</span></span>

  <span data-ttu-id="fe8e9-211">Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe8e9-212">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fe8e9-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fe8e9-213">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-213">Select **File** > **New Solution**.</span></span>

![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="fe8e9-215">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="fe8e9-216">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="fe8e9-217">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="fe8e9-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="fe8e9-218">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="fe8e9-219">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 2.x.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-219">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="fe8e9-220">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-220">Select **Next**.</span></span>

* <span data-ttu-id="fe8e9-221">Присвойте проекту имя **RazorPagesMovie** и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-221">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="fe8e9-223">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="fe8e9-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe8e9-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe8e9-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fe8e9-225">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="fe8e9-226">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="fe8e9-227">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fe8e9-228">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="fe8e9-229">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="fe8e9-230">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="fe8e9-231">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="fe8e9-232">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="fe8e9-234">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="fe8e9-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="fe8e9-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe8e9-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="fe8e9-237">Нажмите клавиши **CTRL-F5**, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="fe8e9-238">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="fe8e9-239">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fe8e9-240">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="fe8e9-241">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="fe8e9-242">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="fe8e9-243">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="fe8e9-245">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="fe8e9-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe8e9-247">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fe8e9-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="fe8e9-248">Нажмите клавиши **Cmd-Opt-F5**, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="fe8e9-249">Visual Studio запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="fe8e9-250">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="fe8e9-251">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="fe8e9-253">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="fe8e9-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Домашняя или индексная страница](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="fe8e9-255">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="fe8e9-255">Examine the project files</span></span>

<span data-ttu-id="fe8e9-256">Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="fe8e9-257">Папка Pages</span><span class="sxs-lookup"><span data-stu-id="fe8e9-257">Pages folder</span></span>

<span data-ttu-id="fe8e9-258">Содержит страницы Razor и вспомогательные файлы.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-258">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="fe8e9-259">Каждая страница Razor — это пара файлов.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-259">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="fe8e9-260">Файл *CSHTML*, содержащий HTML-разметку с кодом C# и синтаксисом Razor.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-260">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="fe8e9-261">Файл *. cshtml.cs*, содержащий код C#, который обрабатывает события страницы.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="fe8e9-262">Имена вспомогательных файлов начинаются с символа подчеркивания.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="fe8e9-263">Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="fe8e9-264">Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="fe8e9-265">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="fe8e9-266">Папка wwwroot</span><span class="sxs-lookup"><span data-stu-id="fe8e9-266">wwwroot folder</span></span>

<span data-ttu-id="fe8e9-267">Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="fe8e9-268">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="fe8e9-269">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="fe8e9-269">appSettings.json</span></span>

<span data-ttu-id="fe8e9-270">Содержит данные конфигурации, например строки подключения.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="fe8e9-271">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="fe8e9-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="fe8e9-272">Program.cs</span></span>

<span data-ttu-id="fe8e9-273">Содержит точку входа для программы.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-273">Contains the entry point for the program.</span></span> <span data-ttu-id="fe8e9-274">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="fe8e9-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="fe8e9-275">Startup.cs</span></span>

<span data-ttu-id="fe8e9-276">Содержит код, который настраивает поведение приложения, например, требуется ли согласие для файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-276">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="fe8e9-277">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="fe8e9-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe8e9-278">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fe8e9-278">Additional resources</span></span>

* [<span data-ttu-id="fe8e9-279">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="fe8e9-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="fe8e9-280">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="fe8e9-280">Next steps</span></span>

<span data-ttu-id="fe8e9-281">Перейдите к следующему учебнику в серии:</span><span class="sxs-lookup"><span data-stu-id="fe8e9-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="fe8e9-282">Добавление модели</span><span class="sxs-lookup"><span data-stu-id="fe8e9-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
