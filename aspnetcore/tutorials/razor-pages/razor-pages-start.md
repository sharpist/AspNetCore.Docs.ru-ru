---
title: Учебник. Начало работы с Razor Pages в ASP.NET Core
author: rick-anderson
description: В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.
ms.author: riande
ms.date: 09/15/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
ms.openlocfilehash: 4d4e50f8acea73859f5e839616f13f90a42291c4
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486230"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="343df-103">Учебник. Начало работы с Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="343df-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="343df-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="343df-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="343df-105">В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="343df-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="343df-106">Дополнительные сведения, предназначенные для разработчиков, которые знакомы с контроллерами и представлениями, см. в статье [Введение в Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="343df-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="343df-107">Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="343df-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="343df-108">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="343df-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="343df-109">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="343df-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="343df-110">Создание веб-приложения Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="343df-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="343df-111">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="343df-111">Run the app.</span></span>
> * <span data-ttu-id="343df-112">Анализ файлов проекта.</span><span class="sxs-lookup"><span data-stu-id="343df-112">Examine the project files.</span></span>

<span data-ttu-id="343df-113">Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, улучшение которого описано в последующих руководствах.</span><span class="sxs-lookup"><span data-stu-id="343df-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="343df-115">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="343df-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="343df-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="343df-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="343df-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343df-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="343df-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="343df-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="343df-119">Создание веб-приложения Razor Pages</span><span class="sxs-lookup"><span data-stu-id="343df-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="343df-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="343df-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="343df-121">Запустите Visual Studio и щелкните **Создать проект**</span><span class="sxs-lookup"><span data-stu-id="343df-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="343df-122">Дополнительные сведения см. в статье [Создание проекта в Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="343df-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![Создание проекта в начальном окне](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="343df-124">В диалоговом окне **Создать проект** выберите **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="343df-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

    ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="343df-126">В диалоговом окне **Настроить новый проект** введите `RazorPagesMovie` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="343df-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="343df-127">Важно присвоить проекту имя *RazorPagesMovie*, включая сопоставление регистра букв, чтобы пространство имени соответствовало при копировании и вставке примера кода.</span><span class="sxs-lookup"><span data-stu-id="343df-127">It's important to name the project *RazorPagesMovie*, including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="343df-128">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="343df-128">Select **Create**.</span></span>

    ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)

1. <span data-ttu-id="343df-130">В диалоговом окне **Создайте веб-приложение ASP.NET Core** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="343df-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="343df-131">В раскрывающихся списках выберите **.NET Core** и **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="343df-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="343df-132">**Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="343df-132">**Web Application**.</span></span>
    1. <span data-ttu-id="343df-133">**Create** (создать).</span><span class="sxs-lookup"><span data-stu-id="343df-133">**Create**.</span></span>

     ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="343df-135">Создается следующий начальный проект:</span><span class="sxs-lookup"><span data-stu-id="343df-135">The following starter project is created:</span></span>

    ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="343df-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343df-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="343df-138">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="343df-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="343df-139">Перейдите в каталог `cd`, в котором находится проект.</span><span class="sxs-lookup"><span data-stu-id="343df-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="343df-140">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="343df-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="343df-141">Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="343df-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="343df-142">Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="343df-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="343df-143">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="343df-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="343df-144">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="343df-144">Select **File** > **New Solution**.</span></span>

    ![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="343df-146">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="343df-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="343df-147">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="343df-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![Выбор шаблона веб-приложения macOS](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="343df-149">В диалоговом окне **Configure the new Web Application** (Настройка нового веб-приложения):</span><span class="sxs-lookup"><span data-stu-id="343df-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="343df-150">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="343df-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="343df-151">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию .NET 5.x.</span><span class="sxs-lookup"><span data-stu-id="343df-151">If presented an option to select a **Target Framework**, select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="343df-152">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="343df-152">Select **Next**.</span></span>

1. <span data-ttu-id="343df-153">Укажите название проекта *RazorPagesMovie* и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="343df-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![Имя проекта macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="343df-155">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="343df-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="343df-156">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="343df-156">Examine the project files</span></span>

<span data-ttu-id="343df-157">Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.</span><span class="sxs-lookup"><span data-stu-id="343df-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="343df-158">Папка Pages</span><span class="sxs-lookup"><span data-stu-id="343df-158">Pages folder</span></span>

<span data-ttu-id="343df-159">Содержит страницы Razor и вспомогательные файлы.</span><span class="sxs-lookup"><span data-stu-id="343df-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="343df-160">Каждая страница Razor — это пара файлов.</span><span class="sxs-lookup"><span data-stu-id="343df-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="343df-161">Файл *.cshtml* с разметкой HTML и кодом C# использует синтаксис Razor.</span><span class="sxs-lookup"><span data-stu-id="343df-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="343df-162">Файл *.cshtml.cs* с кодом C#, который обрабатывает события страницы.</span><span class="sxs-lookup"><span data-stu-id="343df-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="343df-163">Имена вспомогательных файлов начинаются с символа подчеркивания.</span><span class="sxs-lookup"><span data-stu-id="343df-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="343df-164">Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц.</span><span class="sxs-lookup"><span data-stu-id="343df-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="343df-165">Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="343df-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="343df-166">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="343df-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="343df-167">Папка wwwroot</span><span class="sxs-lookup"><span data-stu-id="343df-167">wwwroot folder</span></span>

<span data-ttu-id="343df-168">Содержит статические ресурсы, такие как HTML-файлы, файлы JavaScript и CSS-файлы.</span><span class="sxs-lookup"><span data-stu-id="343df-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="343df-169">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="343df-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="343df-170">Содержит данные конфигурации, например строки подключения.</span><span class="sxs-lookup"><span data-stu-id="343df-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="343df-171">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="343df-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="343df-172">Program.cs</span><span class="sxs-lookup"><span data-stu-id="343df-172">Program.cs</span></span>

<span data-ttu-id="343df-173">Содержит точку входа для приложения.</span><span class="sxs-lookup"><span data-stu-id="343df-173">Contains the entry point for the app.</span></span> <span data-ttu-id="343df-174">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="343df-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="343df-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="343df-175">Startup.cs</span></span>

<span data-ttu-id="343df-176">содержит код, задающий поведение приложения.</span><span class="sxs-lookup"><span data-stu-id="343df-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="343df-177">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="343df-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="343df-178">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="343df-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="343df-179">Далее: Добавление модели</span><span class="sxs-lookup"><span data-stu-id="343df-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="343df-180">В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="343df-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="343df-181">Дополнительные сведения, предназначенные для разработчиков, которые знакомы с контроллерами и представлениями, см. в статье [Введение в Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="343df-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="343df-182">Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="343df-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="343df-183">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="343df-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="343df-184">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="343df-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="343df-185">Создание веб-приложения Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="343df-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="343df-186">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="343df-186">Run the app.</span></span>
> * <span data-ttu-id="343df-187">Анализ файлов проекта.</span><span class="sxs-lookup"><span data-stu-id="343df-187">Examine the project files.</span></span>

<span data-ttu-id="343df-188">Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.</span><span class="sxs-lookup"><span data-stu-id="343df-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="343df-190">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="343df-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="343df-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="343df-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="343df-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343df-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="343df-193">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="343df-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="343df-194">Создание веб-приложения Razor Pages</span><span class="sxs-lookup"><span data-stu-id="343df-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="343df-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="343df-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="343df-196">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="343df-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="343df-197">Создайте веб-приложение ASP.NET Core и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="343df-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="343df-198">![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="343df-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="343df-199">Присвойте проекту имя **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="343df-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="343df-200">Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.</span><span class="sxs-lookup"><span data-stu-id="343df-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="343df-201">![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="343df-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="343df-202">Выберите в раскрывающемся списке пункт **ASP.NET Core 3.1**, затем — **Веб-приложение** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="343df-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="343df-204">Создается следующий начальный проект:</span><span class="sxs-lookup"><span data-stu-id="343df-204">The following starter project is created:</span></span>

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="343df-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343df-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="343df-207">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="343df-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="343df-208">Перейдите в каталог `cd`, в котором находится проект.</span><span class="sxs-lookup"><span data-stu-id="343df-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="343df-209">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="343df-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="343df-210">Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="343df-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="343df-211">Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="343df-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="343df-212">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="343df-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="343df-213">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="343df-213">Select **Yes**.</span></span>

  <span data-ttu-id="343df-214">Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.</span><span class="sxs-lookup"><span data-stu-id="343df-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="343df-215">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="343df-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="343df-216">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="343df-216">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="343df-218">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="343df-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="343df-219">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="343df-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Выбор шаблона веб-приложения macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="343df-221">В диалоговом окне **Configure the new Web Application** (Настройка нового веб-приложения):</span><span class="sxs-lookup"><span data-stu-id="343df-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="343df-222">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="343df-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="343df-223">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 3.x.</span><span class="sxs-lookup"><span data-stu-id="343df-223">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="343df-224">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="343df-224">Select **Next**.</span></span>

* <span data-ttu-id="343df-225">Присвойте проекту имя **RazorPagesMovie** и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="343df-225">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![Имя проекта macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="343df-227">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="343df-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="343df-228">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="343df-228">Examine the project files</span></span>

<span data-ttu-id="343df-229">Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.</span><span class="sxs-lookup"><span data-stu-id="343df-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="343df-230">Папка Pages</span><span class="sxs-lookup"><span data-stu-id="343df-230">Pages folder</span></span>

<span data-ttu-id="343df-231">Содержит страницы Razor и вспомогательные файлы.</span><span class="sxs-lookup"><span data-stu-id="343df-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="343df-232">Каждая страница Razor — это пара файлов.</span><span class="sxs-lookup"><span data-stu-id="343df-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="343df-233">Файл *.cshtml* с разметкой HTML и кодом C# использует синтаксис Razor.</span><span class="sxs-lookup"><span data-stu-id="343df-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="343df-234">Файл *.cshtml.cs* с кодом C#, который обрабатывает события страницы.</span><span class="sxs-lookup"><span data-stu-id="343df-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="343df-235">Имена вспомогательных файлов начинаются с символа подчеркивания.</span><span class="sxs-lookup"><span data-stu-id="343df-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="343df-236">Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц.</span><span class="sxs-lookup"><span data-stu-id="343df-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="343df-237">Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="343df-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="343df-238">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="343df-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="343df-239">Папка wwwroot</span><span class="sxs-lookup"><span data-stu-id="343df-239">wwwroot folder</span></span>

<span data-ttu-id="343df-240">Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы.</span><span class="sxs-lookup"><span data-stu-id="343df-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="343df-241">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="343df-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="343df-242">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="343df-242">appSettings.json</span></span>

<span data-ttu-id="343df-243">Содержит данные конфигурации, например строки подключения.</span><span class="sxs-lookup"><span data-stu-id="343df-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="343df-244">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="343df-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="343df-245">Program.cs</span><span class="sxs-lookup"><span data-stu-id="343df-245">Program.cs</span></span>

<span data-ttu-id="343df-246">Содержит точку входа для программы.</span><span class="sxs-lookup"><span data-stu-id="343df-246">Contains the entry point for the program.</span></span> <span data-ttu-id="343df-247">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="343df-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="343df-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="343df-248">Startup.cs</span></span>

<span data-ttu-id="343df-249">содержит код, задающий поведение приложения.</span><span class="sxs-lookup"><span data-stu-id="343df-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="343df-250">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="343df-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="343df-251">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="343df-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="343df-252">Далее: Добавление модели</span><span class="sxs-lookup"><span data-stu-id="343df-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="343df-253">Это первый учебник из серии.</span><span class="sxs-lookup"><span data-stu-id="343df-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="343df-254">В этой [серии](xref:tutorials/razor-pages/index) приводятся основные сведения о сборке веб-приложения Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="343df-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="343df-255">Дополнительные сведения, предназначенные для разработчиков, которые знакомы с контроллерами и представлениями, см. в статье [Введение в Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="343df-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="343df-256">Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="343df-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="343df-257">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="343df-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="343df-258">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="343df-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="343df-259">Создание веб-приложения Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="343df-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="343df-260">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="343df-260">Run the app.</span></span>
> * <span data-ttu-id="343df-261">Анализ файлов проекта.</span><span class="sxs-lookup"><span data-stu-id="343df-261">Examine the project files.</span></span>

<span data-ttu-id="343df-262">Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.</span><span class="sxs-lookup"><span data-stu-id="343df-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="343df-264">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="343df-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="343df-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="343df-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="343df-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343df-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="343df-267">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="343df-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="343df-268">Создание веб-приложения Razor Pages</span><span class="sxs-lookup"><span data-stu-id="343df-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="343df-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="343df-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="343df-270">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="343df-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="343df-271">Создайте веб-приложение ASP.NET Core и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="343df-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="343df-273">Присвойте проекту имя **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="343df-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="343df-274">Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.</span><span class="sxs-lookup"><span data-stu-id="343df-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="343df-276">Выберите в раскрывающемся списке пункт **ASP.NET Core 2.2**, затем — **Веб-приложение** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="343df-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="343df-278">Создается следующий начальный проект:</span><span class="sxs-lookup"><span data-stu-id="343df-278">The following starter project is created:</span></span>

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="343df-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343df-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="343df-281">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="343df-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="343df-282">Перейдите в каталог `cd`, в котором находится проект.</span><span class="sxs-lookup"><span data-stu-id="343df-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="343df-283">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="343df-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="343df-284">Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="343df-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="343df-285">Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="343df-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="343df-286">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="343df-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="343df-287">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="343df-287">Select **Yes**.</span></span>

  <span data-ttu-id="343df-288">Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.</span><span class="sxs-lookup"><span data-stu-id="343df-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="343df-289">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="343df-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="343df-290">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="343df-290">Select **File** > **New Solution**.</span></span>

![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="343df-292">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="343df-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="343df-293">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="343df-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="343df-294">В диалоговом окне **Configure the new Web Application** (Настройка нового веб-приложения):</span><span class="sxs-lookup"><span data-stu-id="343df-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="343df-295">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="343df-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="343df-296">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 2.x.</span><span class="sxs-lookup"><span data-stu-id="343df-296">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="343df-297">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="343df-297">Select **Next**.</span></span>

* <span data-ttu-id="343df-298">Присвойте проекту имя **RazorPagesMovie** и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="343df-298">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="343df-300">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="343df-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="343df-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="343df-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="343df-302">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="343df-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="343df-303">Запуск приложения с помощью клавиш <kbd>CTRL+F5</kbd> (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="343df-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="343df-304">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="343df-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="343df-305">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="343df-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="343df-306">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="343df-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="343df-307">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="343df-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="343df-308">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="343df-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="343df-309">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="343df-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="343df-310">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="343df-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="343df-311">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="343df-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="343df-313">На следующем рисунке показано приложение после согласия на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="343df-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="343df-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343df-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="343df-316">Нажмите клавиши <kbd>CTRL+F5</kbd>, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="343df-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="343df-317">Запуск приложения с помощью клавиш <kbd>CTRL+F5</kbd> (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="343df-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="343df-318">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="343df-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="343df-319">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), затем — браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="343df-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="343df-320">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="343df-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="343df-321">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="343df-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="343df-322">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="343df-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="343df-323">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="343df-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="343df-324">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="343df-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="343df-326">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="343df-326">The following image shows the app after you give consent to tracking:</span></span>

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="343df-328">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="343df-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="343df-329">Нажмите клавиши **Cmd-Opt-F5**, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="343df-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="343df-330">Запуск приложения с помощью клавиш <kbd>Cmd+Opt+F5</kbd> (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="343df-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="343df-331">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="343df-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="343df-332">Visual Studio запускает [Kestrel](xref:fundamentals/servers/kestrel), затем — браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="343df-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="343df-333">На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="343df-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="343df-334">Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.</span><span class="sxs-lookup"><span data-stu-id="343df-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="343df-336">На следующем рисунке показано приложение после согласия на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="343df-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![Экран "Страница Home (Главная) или страница Index (Индекс)"](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="343df-338">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="343df-338">Examine the project files</span></span>

<span data-ttu-id="343df-339">Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.</span><span class="sxs-lookup"><span data-stu-id="343df-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="343df-340">Папка Pages</span><span class="sxs-lookup"><span data-stu-id="343df-340">Pages folder</span></span>

<span data-ttu-id="343df-341">Содержит страницы Razor и вспомогательные файлы.</span><span class="sxs-lookup"><span data-stu-id="343df-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="343df-342">Каждая страница Razor — это пара файлов.</span><span class="sxs-lookup"><span data-stu-id="343df-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="343df-343">Файл *.cshtml* с разметкой HTML и кодом C# использует синтаксис Razor.</span><span class="sxs-lookup"><span data-stu-id="343df-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="343df-344">Файл *.cshtml.cs* с кодом C#, который обрабатывает события страницы.</span><span class="sxs-lookup"><span data-stu-id="343df-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="343df-345">Имена вспомогательных файлов начинаются с символа подчеркивания.</span><span class="sxs-lookup"><span data-stu-id="343df-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="343df-346">Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц.</span><span class="sxs-lookup"><span data-stu-id="343df-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="343df-347">Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="343df-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="343df-348">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="343df-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="343df-349">Razor Pages являются производными от класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="343df-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="343df-350">Как правило, класс, производный от `PageModel`, называется `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="343df-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="343df-351">Папка wwwroot</span><span class="sxs-lookup"><span data-stu-id="343df-351">wwwroot folder</span></span>

<span data-ttu-id="343df-352">Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы.</span><span class="sxs-lookup"><span data-stu-id="343df-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="343df-353">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="343df-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="343df-354">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="343df-354">appSettings.json</span></span>

<span data-ttu-id="343df-355">Содержит данные конфигурации, например строки подключения.</span><span class="sxs-lookup"><span data-stu-id="343df-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="343df-356">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="343df-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="343df-357">Program.cs</span><span class="sxs-lookup"><span data-stu-id="343df-357">Program.cs</span></span>

<span data-ttu-id="343df-358">Содержит точку входа для программы.</span><span class="sxs-lookup"><span data-stu-id="343df-358">Contains the entry point for the program.</span></span> <span data-ttu-id="343df-359">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="343df-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="343df-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="343df-360">Startup.cs</span></span>

<span data-ttu-id="343df-361">Содержит код, который настраивает поведение приложения, например, требуется ли согласие для файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="343df-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="343df-362">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="343df-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="343df-363">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="343df-363">Additional resources</span></span>

* [<span data-ttu-id="343df-364">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="343df-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="343df-365">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="343df-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="343df-366">Далее: Добавление модели</span><span class="sxs-lookup"><span data-stu-id="343df-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
