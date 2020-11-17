---
title: Учебник. Начало работы с Razor Pages в ASP.NET Core
author: rick-anderson
description: В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.
ms.author: riande
ms.date: 09/15/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: b4dcbe9536107cdc5b0342782abc4bad0b89a8dc
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360968"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="2dc07-103">Учебник. Начало работы с Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2dc07-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="2dc07-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="2dc07-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="2dc07-105">В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2dc07-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="2dc07-106">Дополнительные сведения, предназначенные для разработчиков, которые знакомы с контроллерами и представлениями, см. в статье [Введение в Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="2dc07-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="2dc07-107">Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="2dc07-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="2dc07-108">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2dc07-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="2dc07-109">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="2dc07-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2dc07-110">Создание веб-приложения Razor Pages с помощью команды Create.</span><span class="sxs-lookup"><span data-stu-id="2dc07-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="2dc07-111">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="2dc07-111">Run the app.</span></span>
> * <span data-ttu-id="2dc07-112">Анализ файлов проекта.</span><span class="sxs-lookup"><span data-stu-id="2dc07-112">Examine the project files.</span></span>

<span data-ttu-id="2dc07-113">Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, улучшение которого описано в последующих руководствах.</span><span class="sxs-lookup"><span data-stu-id="2dc07-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="2dc07-115">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="2dc07-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2dc07-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2dc07-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2dc07-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2dc07-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2dc07-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2dc07-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="2dc07-119">Создание веб-приложения Razor Pages с помощью команды Create</span><span class="sxs-lookup"><span data-stu-id="2dc07-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2dc07-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2dc07-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2dc07-121">Запустите Visual Studio и щелкните **Create a new project** (Создать проект).</span><span class="sxs-lookup"><span data-stu-id="2dc07-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="2dc07-122">Дополнительные сведения см. в статье [Создание проекта в Visual Studio](/visualstudio/ide/create-new-project) (с помощью команды Create).</span><span class="sxs-lookup"><span data-stu-id="2dc07-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![Экран "Создание проекта из начального окна с помощью Create"](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="2dc07-124">В диалоговом окне **Create a new project** (Создать проект) выберите **Веб-приложение ASP.NET Core**, а затем — **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

    ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="2dc07-126">В диалоговом окне **Настроить новый проект** введите `RazorPagesMovie` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="2dc07-127">Важно присвоить проекту имя *RazorPagesMovie*, включая сопоставление регистра букв, чтобы пространство имени соответствовало при копировании и вставке примера кода.</span><span class="sxs-lookup"><span data-stu-id="2dc07-127">It's important to name the project *RazorPagesMovie*, including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="2dc07-128">Выберите **Create**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-128">Select **Create**.</span></span>

    ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)

1. <span data-ttu-id="2dc07-130">В диалоговом окне выберите **Create a new ASP.NET Core web application** (Создать веб-приложение ASP.NET Core), а затем выберите</span><span class="sxs-lookup"><span data-stu-id="2dc07-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="2dc07-131">В раскрывающихся списках выберите **.NET Core** и **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="2dc07-132">**Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-132">**Web Application**.</span></span>
    1. <span data-ttu-id="2dc07-133">**Create**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-133">**Create**.</span></span>

     ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="2dc07-135">Создается следующий начальный проект:</span><span class="sxs-lookup"><span data-stu-id="2dc07-135">The following starter project is created:</span></span>

    ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2dc07-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2dc07-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2dc07-138">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2dc07-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="2dc07-139">Перейдите в каталог `cd`, в котором находится проект.</span><span class="sxs-lookup"><span data-stu-id="2dc07-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="2dc07-140">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="2dc07-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="2dc07-141">Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="2dc07-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="2dc07-142">Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2dc07-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2dc07-143">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2dc07-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2dc07-144">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-144">Select **File** > **New Solution**.</span></span>

    ![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="2dc07-146">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="2dc07-147">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![Выбор шаблона веб-приложения macOS](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="2dc07-149">В диалоговом окне **Configure the new Web Application** (Настройка нового веб-приложения):</span><span class="sxs-lookup"><span data-stu-id="2dc07-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="2dc07-150">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="2dc07-151">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию .NET 5.x.</span><span class="sxs-lookup"><span data-stu-id="2dc07-151">If presented an option to select a **Target Framework**, select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="2dc07-152">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-152">Select **Next**.</span></span>

1. <span data-ttu-id="2dc07-153">Назовите проект *RazorPagesMovie* и выберите **Create** .</span><span class="sxs-lookup"><span data-stu-id="2dc07-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![Имя проекта macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="2dc07-155">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="2dc07-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="2dc07-156">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="2dc07-156">Examine the project files</span></span>

<span data-ttu-id="2dc07-157">Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.</span><span class="sxs-lookup"><span data-stu-id="2dc07-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="2dc07-158">Папка Pages</span><span class="sxs-lookup"><span data-stu-id="2dc07-158">Pages folder</span></span>

<span data-ttu-id="2dc07-159">Содержит страницы Razor и вспомогательные файлы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="2dc07-160">Каждая страница Razor — это пара файлов.</span><span class="sxs-lookup"><span data-stu-id="2dc07-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="2dc07-161">Файл *.cshtml* с разметкой HTML и кодом C# использует синтаксис Razor.</span><span class="sxs-lookup"><span data-stu-id="2dc07-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="2dc07-162">Файл *.cshtml.cs* с кодом C#, который обрабатывает события страницы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="2dc07-163">Имена вспомогательных файлов начинаются с символа подчеркивания.</span><span class="sxs-lookup"><span data-stu-id="2dc07-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="2dc07-164">Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц.</span><span class="sxs-lookup"><span data-stu-id="2dc07-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="2dc07-165">Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="2dc07-166">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="2dc07-167">Папка wwwroot</span><span class="sxs-lookup"><span data-stu-id="2dc07-167">wwwroot folder</span></span>

<span data-ttu-id="2dc07-168">Содержит статические ресурсы, такие как HTML-файлы, файлы JavaScript и CSS-файлы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="2dc07-169">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="2dc07-170">Содержит данные конфигурации, например строки подключения.</span><span class="sxs-lookup"><span data-stu-id="2dc07-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="2dc07-171">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="2dc07-172">Program.cs</span><span class="sxs-lookup"><span data-stu-id="2dc07-172">Program.cs</span></span>

<span data-ttu-id="2dc07-173">Содержит точку входа для приложения.</span><span class="sxs-lookup"><span data-stu-id="2dc07-173">Contains the entry point for the app.</span></span> <span data-ttu-id="2dc07-174">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="2dc07-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2dc07-175">Startup.cs</span></span>

<span data-ttu-id="2dc07-176">содержит код, задающий поведение приложения.</span><span class="sxs-lookup"><span data-stu-id="2dc07-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="2dc07-177">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2dc07-178">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="2dc07-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2dc07-179">Далее: Добавление модели</span><span class="sxs-lookup"><span data-stu-id="2dc07-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="2dc07-180">В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2dc07-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="2dc07-181">Дополнительные сведения, предназначенные для разработчиков, которые знакомы с контроллерами и представлениями, см. в статье [Введение в Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="2dc07-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="2dc07-182">Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="2dc07-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="2dc07-183">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2dc07-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="2dc07-184">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="2dc07-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2dc07-185">Создание веб-приложения Razor Pages с помощью команды Create.</span><span class="sxs-lookup"><span data-stu-id="2dc07-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="2dc07-186">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="2dc07-186">Run the app.</span></span>
> * <span data-ttu-id="2dc07-187">Анализ файлов проекта.</span><span class="sxs-lookup"><span data-stu-id="2dc07-187">Examine the project files.</span></span>

<span data-ttu-id="2dc07-188">Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.</span><span class="sxs-lookup"><span data-stu-id="2dc07-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="2dc07-190">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="2dc07-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2dc07-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2dc07-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2dc07-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2dc07-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2dc07-193">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2dc07-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="2dc07-194">Создание веб-приложения Razor Pages с помощью команды Create</span><span class="sxs-lookup"><span data-stu-id="2dc07-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2dc07-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2dc07-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2dc07-196">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2dc07-197">Создайте веб-приложение ASP.NET Core с мощью командыCreate и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="2dc07-198">![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="2dc07-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="2dc07-199">Присвойте проекту имя **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="2dc07-200">Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.</span><span class="sxs-lookup"><span data-stu-id="2dc07-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="2dc07-201">![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="2dc07-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="2dc07-202">Выберите в раскрывающемся списке пункты **ASP.NET Core 3.1** и **Веб-приложение**, а затем нажмите кнопку **Create** (Создать).</span><span class="sxs-lookup"><span data-stu-id="2dc07-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="2dc07-204">Создается следующий начальный проект:</span><span class="sxs-lookup"><span data-stu-id="2dc07-204">The following starter project is created:</span></span>

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2dc07-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2dc07-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2dc07-207">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2dc07-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="2dc07-208">Перейдите в каталог `cd`, в котором находится проект.</span><span class="sxs-lookup"><span data-stu-id="2dc07-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="2dc07-209">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="2dc07-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="2dc07-210">Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="2dc07-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="2dc07-211">Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2dc07-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="2dc07-212">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="2dc07-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="2dc07-213">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-213">Select **Yes**.</span></span>

  <span data-ttu-id="2dc07-214">Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.</span><span class="sxs-lookup"><span data-stu-id="2dc07-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2dc07-215">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2dc07-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2dc07-216">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-216">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2dc07-218">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="2dc07-219">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Выбор шаблона веб-приложения macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="2dc07-221">В диалоговом окне **Configure the new Web Application** (Настройка нового веб-приложения):</span><span class="sxs-lookup"><span data-stu-id="2dc07-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="2dc07-222">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="2dc07-223">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 3.x.</span><span class="sxs-lookup"><span data-stu-id="2dc07-223">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="2dc07-224">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-224">Select **Next**.</span></span>

* <span data-ttu-id="2dc07-225">Назовите проект **RazorPagesMovie**, а затем выберите **Create** .</span><span class="sxs-lookup"><span data-stu-id="2dc07-225">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![Имя проекта macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="2dc07-227">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="2dc07-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="2dc07-228">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="2dc07-228">Examine the project files</span></span>

<span data-ttu-id="2dc07-229">Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.</span><span class="sxs-lookup"><span data-stu-id="2dc07-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="2dc07-230">Папка Pages</span><span class="sxs-lookup"><span data-stu-id="2dc07-230">Pages folder</span></span>

<span data-ttu-id="2dc07-231">Содержит страницы Razor и вспомогательные файлы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="2dc07-232">Каждая страница Razor — это пара файлов.</span><span class="sxs-lookup"><span data-stu-id="2dc07-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="2dc07-233">Файл *.cshtml* с разметкой HTML и кодом C# использует синтаксис Razor.</span><span class="sxs-lookup"><span data-stu-id="2dc07-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="2dc07-234">Файл *.cshtml.cs* с кодом C#, который обрабатывает события страницы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="2dc07-235">Имена вспомогательных файлов начинаются с символа подчеркивания.</span><span class="sxs-lookup"><span data-stu-id="2dc07-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="2dc07-236">Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц.</span><span class="sxs-lookup"><span data-stu-id="2dc07-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="2dc07-237">Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="2dc07-238">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="2dc07-239">Папка wwwroot</span><span class="sxs-lookup"><span data-stu-id="2dc07-239">wwwroot folder</span></span>

<span data-ttu-id="2dc07-240">Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="2dc07-241">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="2dc07-242">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="2dc07-242">appSettings.json</span></span>

<span data-ttu-id="2dc07-243">Содержит данные конфигурации, например строки подключения.</span><span class="sxs-lookup"><span data-stu-id="2dc07-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="2dc07-244">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="2dc07-245">Program.cs</span><span class="sxs-lookup"><span data-stu-id="2dc07-245">Program.cs</span></span>

<span data-ttu-id="2dc07-246">Содержит точку входа для программы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-246">Contains the entry point for the program.</span></span> <span data-ttu-id="2dc07-247">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="2dc07-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2dc07-248">Startup.cs</span></span>

<span data-ttu-id="2dc07-249">содержит код, задающий поведение приложения.</span><span class="sxs-lookup"><span data-stu-id="2dc07-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="2dc07-250">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2dc07-251">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="2dc07-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2dc07-252">Далее: Добавление модели</span><span class="sxs-lookup"><span data-stu-id="2dc07-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2dc07-253">Это первый учебник из серии.</span><span class="sxs-lookup"><span data-stu-id="2dc07-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="2dc07-254">В этой [серии](xref:tutorials/razor-pages/index) приводятся основные сведения о сборке веб-приложения Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2dc07-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="2dc07-255">Дополнительные сведения, предназначенные для разработчиков, которые знакомы с контроллерами и представлениями, см. в статье [Введение в Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="2dc07-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="2dc07-256">Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="2dc07-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="2dc07-257">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2dc07-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="2dc07-258">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="2dc07-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2dc07-259">Создание веб-приложения Razor Pages с помощью команды Create.</span><span class="sxs-lookup"><span data-stu-id="2dc07-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="2dc07-260">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="2dc07-260">Run the app.</span></span>
> * <span data-ttu-id="2dc07-261">Анализ файлов проекта.</span><span class="sxs-lookup"><span data-stu-id="2dc07-261">Examine the project files.</span></span>

<span data-ttu-id="2dc07-262">Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.</span><span class="sxs-lookup"><span data-stu-id="2dc07-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="2dc07-264">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="2dc07-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2dc07-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2dc07-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2dc07-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2dc07-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2dc07-267">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2dc07-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="2dc07-268">Создание веб-приложения Razor Pages с помощью команды Create</span><span class="sxs-lookup"><span data-stu-id="2dc07-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2dc07-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2dc07-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2dc07-270">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="2dc07-271">Создайте веб-приложение ASP.NET Core с мощью командыCreate и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="2dc07-273">Присвойте проекту имя **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="2dc07-274">Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.</span><span class="sxs-lookup"><span data-stu-id="2dc07-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="2dc07-276">Выберите в раскрывающемся списке пункты **ASP.NET Core 2.2** и **Веб-приложение**, а затем нажмите кнопку **Create** (Создать).</span><span class="sxs-lookup"><span data-stu-id="2dc07-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="2dc07-278">Создается следующий начальный проект:</span><span class="sxs-lookup"><span data-stu-id="2dc07-278">The following starter project is created:</span></span>

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2dc07-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2dc07-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2dc07-281">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2dc07-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="2dc07-282">Перейдите в каталог `cd`, в котором находится проект.</span><span class="sxs-lookup"><span data-stu-id="2dc07-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="2dc07-283">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="2dc07-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="2dc07-284">Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="2dc07-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="2dc07-285">Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2dc07-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="2dc07-286">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="2dc07-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="2dc07-287">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-287">Select **Yes**.</span></span>

  <span data-ttu-id="2dc07-288">Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.</span><span class="sxs-lookup"><span data-stu-id="2dc07-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2dc07-289">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2dc07-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2dc07-290">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-290">Select **File** > **New Solution**.</span></span>

![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2dc07-292">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="2dc07-293">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="2dc07-294">В диалоговом окне **Configure the new Web Application** (Настройка нового веб-приложения):</span><span class="sxs-lookup"><span data-stu-id="2dc07-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="2dc07-295">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="2dc07-296">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 2.x.</span><span class="sxs-lookup"><span data-stu-id="2dc07-296">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="2dc07-297">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2dc07-297">Select **Next**.</span></span>

* <span data-ttu-id="2dc07-298">Назовите проект **RazorPagesMovie**, а затем выберите **Create** .</span><span class="sxs-lookup"><span data-stu-id="2dc07-298">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="2dc07-300">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="2dc07-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2dc07-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2dc07-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2dc07-302">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="2dc07-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="2dc07-303">Запуск приложения с помощью клавиш <kbd>CTRL+F5</kbd> (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="2dc07-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2dc07-304">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="2dc07-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>


  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="2dc07-305">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="2dc07-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2dc07-306">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2dc07-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2dc07-307">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="2dc07-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="2dc07-308">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="2dc07-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="2dc07-309">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="2dc07-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="2dc07-310">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="2dc07-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2dc07-311">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="2dc07-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="2dc07-313">На следующем рисунке показано приложение после согласия на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="2dc07-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="2dc07-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2dc07-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="2dc07-316">Нажмите клавиши <kbd>CTRL+F5</kbd>, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="2dc07-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="2dc07-317">Запуск приложения с помощью клавиш <kbd>CTRL+F5</kbd> (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="2dc07-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2dc07-318">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="2dc07-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="2dc07-319">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), затем — браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2dc07-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="2dc07-320">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2dc07-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2dc07-321">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="2dc07-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="2dc07-322">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="2dc07-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="2dc07-323">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="2dc07-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2dc07-324">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="2dc07-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="2dc07-326">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="2dc07-326">The following image shows the app after you give consent to tracking:</span></span>

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2dc07-328">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2dc07-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2dc07-329">Нажмите клавиши **Cmd-Opt-F5**, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="2dc07-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2dc07-330">Запуск приложения с помощью клавиш <kbd>Cmd+Opt+F5</kbd> (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="2dc07-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2dc07-331">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="2dc07-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="2dc07-332">Visual Studio запускает [Kestrel](xref:fundamentals/servers/kestrel), затем — браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2dc07-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="2dc07-333">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="2dc07-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2dc07-334">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="2dc07-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="2dc07-336">На следующем рисунке показано приложение после согласия на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="2dc07-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="2dc07-338">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="2dc07-338">Examine the project files</span></span>

<span data-ttu-id="2dc07-339">Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.</span><span class="sxs-lookup"><span data-stu-id="2dc07-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="2dc07-340">Папка Pages</span><span class="sxs-lookup"><span data-stu-id="2dc07-340">Pages folder</span></span>

<span data-ttu-id="2dc07-341">Содержит страницы Razor и вспомогательные файлы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="2dc07-342">Каждая страница Razor — это пара файлов.</span><span class="sxs-lookup"><span data-stu-id="2dc07-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="2dc07-343">Файл *.cshtml* с разметкой HTML и кодом C# использует синтаксис Razor.</span><span class="sxs-lookup"><span data-stu-id="2dc07-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="2dc07-344">Файл *.cshtml.cs* с кодом C#, который обрабатывает события страницы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="2dc07-345">Имена вспомогательных файлов начинаются с символа подчеркивания.</span><span class="sxs-lookup"><span data-stu-id="2dc07-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="2dc07-346">Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц.</span><span class="sxs-lookup"><span data-stu-id="2dc07-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="2dc07-347">Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="2dc07-348">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="2dc07-349">Razor Pages являются производными от класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="2dc07-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="2dc07-350">Как правило, класс, производный от `PageModel`, называется `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="2dc07-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="2dc07-351">Папка wwwroot</span><span class="sxs-lookup"><span data-stu-id="2dc07-351">wwwroot folder</span></span>

<span data-ttu-id="2dc07-352">Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="2dc07-353">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="2dc07-354">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="2dc07-354">appSettings.json</span></span>

<span data-ttu-id="2dc07-355">Содержит данные конфигурации, например строки подключения.</span><span class="sxs-lookup"><span data-stu-id="2dc07-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="2dc07-356">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="2dc07-357">Program.cs</span><span class="sxs-lookup"><span data-stu-id="2dc07-357">Program.cs</span></span>

<span data-ttu-id="2dc07-358">Содержит точку входа для программы.</span><span class="sxs-lookup"><span data-stu-id="2dc07-358">Contains the entry point for the program.</span></span> <span data-ttu-id="2dc07-359">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="2dc07-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2dc07-360">Startup.cs</span></span>

<span data-ttu-id="2dc07-361">Содержит код, который настраивает поведение приложения, например, требуется ли согласие для файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="2dc07-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="2dc07-362">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="2dc07-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2dc07-363">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2dc07-363">Additional resources</span></span>

* [<span data-ttu-id="2dc07-364">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="2dc07-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="2dc07-365">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="2dc07-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2dc07-366">Далее: Добавление модели</span><span class="sxs-lookup"><span data-stu-id="2dc07-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
