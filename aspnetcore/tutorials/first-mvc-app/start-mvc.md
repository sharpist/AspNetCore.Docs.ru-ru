---
title: Начало работы с MVC ASP.NET Core
author: rick-anderson
description: Сведения о начале работы с MVC ASP.NET Core.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c2b76b59ae775b9268fa77019bf8420e5e4108b6
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452302"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="2e932-103">Начало работы с MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2e932-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="2e932-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="2e932-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="2e932-105">В этом учебнике приводятся основные сведения о веб-приложении MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e932-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="2e932-106">Это приложение служит для управления базой данных названий фильмов.</span><span class="sxs-lookup"><span data-stu-id="2e932-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="2e932-107">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="2e932-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2e932-108">Создание веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="2e932-108">Create a web app.</span></span>
> * <span data-ttu-id="2e932-109">Добавление модели и формирование шаблона.</span><span class="sxs-lookup"><span data-stu-id="2e932-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="2e932-110">Работа с базой данных.</span><span class="sxs-lookup"><span data-stu-id="2e932-110">Work with a database.</span></span>
> * <span data-ttu-id="2e932-111">Добавление поиска и проверки.</span><span class="sxs-lookup"><span data-stu-id="2e932-111">Add search and validation.</span></span>

<span data-ttu-id="2e932-112">В конечном итоге вы получите приложение, позволяющее управлять данными фильмов и отображать их.</span><span class="sxs-lookup"><span data-stu-id="2e932-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="2e932-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="2e932-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e932-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e932-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2e932-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e932-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e932-116">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2e932-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="2e932-117">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="2e932-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e932-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e932-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2e932-119">В Visual Studio выберите **Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="2e932-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="2e932-120">Выберите пункт **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2e932-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Новое веб-приложение ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="2e932-122">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2e932-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="2e932-123">Имя **MvcMovie** необходимо присвоить для того, чтобы при копировании кода пространства имен совпали.</span><span class="sxs-lookup"><span data-stu-id="2e932-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Новое веб-приложение ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="2e932-125">Выберите **Веб-приложение (модель-представление-контроллер)** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2e932-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="2e932-126">Диалоговое окно нового проекта, .NET Core в левой области, веб-узел ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2e932-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="2e932-127">В Visual Studio используется только что созданный вами шаблон по умолчанию для проекта MVC.</span><span class="sxs-lookup"><span data-stu-id="2e932-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="2e932-128">Чтобы приложение стало рабочим, осталось только указать имя проекта и выбрать несколько параметров.</span><span class="sxs-lookup"><span data-stu-id="2e932-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="2e932-129">Это простой начальный проект.</span><span class="sxs-lookup"><span data-stu-id="2e932-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2e932-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e932-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2e932-131">Для работы с этим руководством требуется знание VS Code.</span><span class="sxs-lookup"><span data-stu-id="2e932-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="2e932-132">Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="2e932-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="2e932-133">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2e932-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2e932-134">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="2e932-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="2e932-135">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2e932-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="2e932-136">Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="2e932-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="2e932-137">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="2e932-137">Select **Yes**</span></span>

  * <span data-ttu-id="2e932-138">`dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="2e932-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="2e932-139">`code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2e932-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e932-140">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2e932-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2e932-141">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="2e932-141">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2e932-143">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2e932-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="2e932-144">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2e932-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Выбор шаблона веб-приложения macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="2e932-146">Подтвердите следующие конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2e932-146">Confirm the following configurations:</span></span>

  * <span data-ttu-id="2e932-147">Для параметра **Целевая платформа** задано значение **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="2e932-147">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="2e932-148">Для параметра **Проверка подлинности** задано значение **Без проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="2e932-148">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="2e932-149">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2e932-149">Select **Next**.</span></span>

  ![Выбор .NET Core 3.1 для macOS](start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="2e932-151">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2e932-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Имя проекта macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="2e932-153">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="2e932-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e932-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e932-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2e932-155">Нажмите **CTRl+F5**, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="2e932-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="2e932-156">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="2e932-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2e932-157">Обратите внимание на то, что в адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2e932-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2e932-158">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="2e932-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2e932-159">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="2e932-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="2e932-160">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="2e932-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2e932-161">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="2e932-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="2e932-162">Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.</span><span class="sxs-lookup"><span data-stu-id="2e932-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Меню отладки](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="2e932-164">Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="2e932-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="2e932-166">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="2e932-166">The following image shows the app:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2e932-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e932-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2e932-169">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="2e932-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="2e932-170">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2e932-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="2e932-171">В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2e932-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="2e932-172">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="2e932-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2e932-173">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="2e932-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="2e932-174">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="2e932-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2e932-175">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро обновлять страницу и просматривать изменения.</span><span class="sxs-lookup"><span data-stu-id="2e932-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e932-177">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2e932-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2e932-178">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="2e932-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="2e932-179">Visual Studio для Mac запустит сервер [Kestrel](xref:fundamentals/servers/index#kestrel), откроет браузер и перейдет к `http://localhost:port`, где *port* — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="2e932-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2e932-180">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2e932-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2e932-181">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="2e932-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2e932-182">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="2e932-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="2e932-183">При запуске приложения вы увидите другой номер порта.</span><span class="sxs-lookup"><span data-stu-id="2e932-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="2e932-184">В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.</span><span class="sxs-lookup"><span data-stu-id="2e932-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="2e932-185">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="2e932-185">The following image shows the app:</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="2e932-187">В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.</span><span class="sxs-lookup"><span data-stu-id="2e932-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2e932-188">Вперед</span><span class="sxs-lookup"><span data-stu-id="2e932-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="2e932-189">В этом учебнике приводятся основные сведения о веб-приложении MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e932-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="2e932-190">Это приложение служит для управления базой данных названий фильмов.</span><span class="sxs-lookup"><span data-stu-id="2e932-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="2e932-191">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="2e932-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2e932-192">Создание веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="2e932-192">Create a web app.</span></span>
> * <span data-ttu-id="2e932-193">Добавление модели и формирование шаблона.</span><span class="sxs-lookup"><span data-stu-id="2e932-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="2e932-194">Работа с базой данных.</span><span class="sxs-lookup"><span data-stu-id="2e932-194">Work with a database.</span></span>
> * <span data-ttu-id="2e932-195">Добавление поиска и проверки.</span><span class="sxs-lookup"><span data-stu-id="2e932-195">Add search and validation.</span></span>

<span data-ttu-id="2e932-196">В конечном итоге вы получите приложение, позволяющее управлять данными фильмов и отображать их.</span><span class="sxs-lookup"><span data-stu-id="2e932-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="2e932-197">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="2e932-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e932-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e932-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2e932-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e932-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e932-200">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2e932-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="2e932-201">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="2e932-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e932-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e932-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2e932-203">В Visual Studio выберите **Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="2e932-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="2e932-204">Выберите пункт **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2e932-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Новое веб-приложение ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="2e932-206">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2e932-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="2e932-207">Имя **MvcMovie** необходимо присвоить для того, чтобы при копировании кода пространства имен совпали.</span><span class="sxs-lookup"><span data-stu-id="2e932-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Новое веб-приложение ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="2e932-209">Выберите **Веб-приложение (модель-представление-контроллер)** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2e932-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="2e932-210">Диалоговое окно нового проекта, .NET Core в левой области, веб-узел ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2e932-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="2e932-211">В Visual Studio используется только что созданный вами шаблон по умолчанию для проекта MVC.</span><span class="sxs-lookup"><span data-stu-id="2e932-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="2e932-212">Чтобы приложение стало рабочим, осталось только указать имя проекта и выбрать несколько параметров.</span><span class="sxs-lookup"><span data-stu-id="2e932-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="2e932-213">Это простой начальный проект и хорошая стартовая точка.</span><span class="sxs-lookup"><span data-stu-id="2e932-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2e932-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e932-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2e932-215">Для работы с этим руководством требуется знание VS Code.</span><span class="sxs-lookup"><span data-stu-id="2e932-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="2e932-216">Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="2e932-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="2e932-217">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2e932-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2e932-218">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="2e932-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="2e932-219">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2e932-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="2e932-220">Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="2e932-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="2e932-221">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="2e932-221">Select **Yes**</span></span>

  * <span data-ttu-id="2e932-222">`dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="2e932-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="2e932-223">`code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2e932-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e932-224">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2e932-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2e932-225">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="2e932-225">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2e932-227">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2e932-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="2e932-228">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2e932-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="2e932-229">В диалоговом окне **Настройка нового веб-API ASP.NET Core** оставьте установленное по умолчанию значение для параметра **Целевая платформа**, то есть **.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="2e932-229">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![Выбор .NET Core 2.2 для macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="2e932-231">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2e932-231">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="2e932-232">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="2e932-232">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e932-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e932-233">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2e932-234">Нажмите **CTRl+F5**, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="2e932-234">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="2e932-235">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="2e932-235">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2e932-236">Обратите внимание на то, что в адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2e932-236">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2e932-237">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="2e932-237">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2e932-238">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="2e932-238">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="2e932-239">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="2e932-239">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2e932-240">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="2e932-240">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="2e932-241">Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.</span><span class="sxs-lookup"><span data-stu-id="2e932-241">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Меню отладки](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="2e932-243">Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="2e932-243">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="2e932-245">Нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="2e932-245">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="2e932-246">Это приложение не отслеживает персональные данные.</span><span class="sxs-lookup"><span data-stu-id="2e932-246">This app doesn't track personal information.</span></span> <span data-ttu-id="2e932-247">Созданный шаблоном код включает ресурсы для соблюдения [Общего регламента по защите данных (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="2e932-247">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/privacy.png)

  <span data-ttu-id="2e932-249">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="2e932-249">The following image shows the app after accepting tracking:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2e932-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e932-251">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2e932-252">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="2e932-252">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="2e932-253">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2e932-253">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="2e932-254">В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2e932-254">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="2e932-255">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="2e932-255">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2e932-256">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="2e932-256">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="2e932-257">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="2e932-257">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2e932-258">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро обновлять страницу и просматривать изменения.</span><span class="sxs-lookup"><span data-stu-id="2e932-258">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="2e932-259">Нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="2e932-259">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="2e932-260">Это приложение не отслеживает персональные данные.</span><span class="sxs-lookup"><span data-stu-id="2e932-260">This app doesn't track personal information.</span></span> <span data-ttu-id="2e932-261">Созданный шаблоном код включает ресурсы для соблюдения [Общего регламента по защите данных (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="2e932-261">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/privacy.png)

  <span data-ttu-id="2e932-263">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="2e932-263">The following image shows the app after accepting tracking:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e932-265">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2e932-265">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2e932-266">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="2e932-266">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="2e932-267">Visual Studio для Mac запустит сервер [Kestrel](xref:fundamentals/servers/index#kestrel), откроет браузер и перейдет к `http://localhost:port`, где *port* — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="2e932-267">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2e932-268">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2e932-268">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2e932-269">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="2e932-269">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2e932-270">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="2e932-270">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="2e932-271">При запуске приложения вы увидите другой номер порта.</span><span class="sxs-lookup"><span data-stu-id="2e932-271">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="2e932-272">В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.</span><span class="sxs-lookup"><span data-stu-id="2e932-272">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="2e932-273">Нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="2e932-273">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="2e932-274">Это приложение не отслеживает персональные данные.</span><span class="sxs-lookup"><span data-stu-id="2e932-274">This app doesn't track personal information.</span></span> <span data-ttu-id="2e932-275">Созданный шаблоном код включает ресурсы для соблюдения [Общего регламента по защите данных (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="2e932-275">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="2e932-277">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="2e932-277">The following image shows the app after accepting tracking:</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="2e932-279">В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.</span><span class="sxs-lookup"><span data-stu-id="2e932-279">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2e932-280">Вперед</span><span class="sxs-lookup"><span data-stu-id="2e932-280">Next</span></span>](adding-controller.md)

::: moniker-end
