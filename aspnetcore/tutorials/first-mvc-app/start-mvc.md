---
title: Начало работы с MVC ASP.NET Core
author: rick-anderson
description: Сведения о начале работы с MVC ASP.NET Core.
ms.author: riande
ms.date: 10/16/2019
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: cf17aaf8eff342c378536d4f635e09b936459bee
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052920"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="5ac81-103">Начало работы с MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5ac81-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="5ac81-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="5ac81-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="5ac81-105">В этом учебнике приводятся основные сведения о веб-приложении MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5ac81-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="5ac81-106">Это приложение служит для управления базой данных названий фильмов.</span><span class="sxs-lookup"><span data-stu-id="5ac81-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="5ac81-107">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="5ac81-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5ac81-108">Создание веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="5ac81-108">Create a web app.</span></span>
> * <span data-ttu-id="5ac81-109">Добавление модели и формирование шаблона.</span><span class="sxs-lookup"><span data-stu-id="5ac81-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="5ac81-110">Работа с базой данных.</span><span class="sxs-lookup"><span data-stu-id="5ac81-110">Work with a database.</span></span>
> * <span data-ttu-id="5ac81-111">Добавление поиска и проверки.</span><span class="sxs-lookup"><span data-stu-id="5ac81-111">Add search and validation.</span></span>

<span data-ttu-id="5ac81-112">В конечном итоге вы получите приложение, позволяющее управлять данными фильмов и отображать их.</span><span class="sxs-lookup"><span data-stu-id="5ac81-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="5ac81-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="5ac81-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5ac81-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ac81-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5ac81-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5ac81-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5ac81-116">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5ac81-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="5ac81-117">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="5ac81-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5ac81-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ac81-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5ac81-119">В Visual Studio выберите **Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="5ac81-120">Выберите **Веб-приложение ASP.NET Core**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-120">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![Новое веб-приложение ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="5ac81-122">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="5ac81-123">Имя **MvcMovie** необходимо присвоить для того, чтобы при копировании кода пространства имен совпали.</span><span class="sxs-lookup"><span data-stu-id="5ac81-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Новое веб-приложение ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="5ac81-125">Выберите **Веб-приложение (модель — представление — контроллер)** .</span><span class="sxs-lookup"><span data-stu-id="5ac81-125">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="5ac81-126">В раскрывающихся списках выберите пункты **.NET Core** и **ASP.NET Core 3.1** , а затем щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-126">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1** , then select **Create**.</span></span>

![<span data-ttu-id="5ac81-127">Диалоговое окно нового проекта, .NET Core в левой области, веб-узел ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5ac81-127">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="5ac81-128">В Visual Studio используется только что созданный вами шаблон по умолчанию для проекта MVC.</span><span class="sxs-lookup"><span data-stu-id="5ac81-128">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="5ac81-129">Чтобы приложение стало рабочим, осталось только указать имя проекта и выбрать несколько параметров.</span><span class="sxs-lookup"><span data-stu-id="5ac81-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="5ac81-130">Это простой начальный проект.</span><span class="sxs-lookup"><span data-stu-id="5ac81-130">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5ac81-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5ac81-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5ac81-132">Для работы с этим руководством требуется знание VS Code.</span><span class="sxs-lookup"><span data-stu-id="5ac81-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="5ac81-133">Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="5ac81-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="5ac81-134">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5ac81-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5ac81-135">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="5ac81-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="5ac81-136">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="5ac81-136">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="5ac81-137">Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="5ac81-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="5ac81-138">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-138">Select **Yes**</span></span>

  * <span data-ttu-id="5ac81-139">`dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="5ac81-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="5ac81-140">`code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5ac81-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5ac81-141">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5ac81-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5ac81-142">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-142">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="5ac81-144">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-144">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="5ac81-145">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-145">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Выбор шаблона веб-приложения macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="5ac81-147">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="5ac81-147">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="5ac81-148">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-148">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="5ac81-149">Если отобразится запрос на выбор **целевой платформы** , выберите последнюю версию 3.x.</span><span class="sxs-lookup"><span data-stu-id="5ac81-149">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="5ac81-150">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-150">Select **Next**.</span></span>

* <span data-ttu-id="5ac81-151">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-151">Name the project **MvcMovie** , and then select **Create**.</span></span>

  ![Имя проекта macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="5ac81-153">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="5ac81-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5ac81-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ac81-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5ac81-155">Нажмите **CTRl+F5** , чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="5ac81-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="5ac81-156">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="5ac81-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5ac81-157">Обратите внимание на то, что в адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5ac81-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5ac81-158">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5ac81-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5ac81-159">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="5ac81-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="5ac81-160">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="5ac81-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5ac81-161">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="5ac81-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="5ac81-162">Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.</span><span class="sxs-lookup"><span data-stu-id="5ac81-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Меню отладки](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="5ac81-164">Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="5ac81-166">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="5ac81-166">The following image shows the app:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5ac81-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5ac81-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5ac81-169">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="5ac81-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="5ac81-170">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="5ac81-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="5ac81-171">В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5ac81-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="5ac81-172">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5ac81-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="5ac81-173">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5ac81-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="5ac81-174">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="5ac81-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5ac81-175">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро обновлять страницу и просматривать изменения.</span><span class="sxs-lookup"><span data-stu-id="5ac81-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5ac81-177">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5ac81-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5ac81-178">Выберите **Выполнить** > **Запуск без отладки** , чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="5ac81-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="5ac81-179">Visual Studio для Mac запустит сервер [Kestrel](xref:fundamentals/servers/index#kestrel), откроет браузер и перейдет к `http://localhost:port`, где *port*  — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="5ac81-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="5ac81-180">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5ac81-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5ac81-181">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5ac81-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5ac81-182">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="5ac81-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5ac81-183">При запуске приложения вы увидите другой номер порта.</span><span class="sxs-lookup"><span data-stu-id="5ac81-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5ac81-184">В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.</span><span class="sxs-lookup"><span data-stu-id="5ac81-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="5ac81-185">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="5ac81-185">The following image shows the app:</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="5ac81-187">В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.</span><span class="sxs-lookup"><span data-stu-id="5ac81-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5ac81-188">Вперед</span><span class="sxs-lookup"><span data-stu-id="5ac81-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="5ac81-189">В этом учебнике приводятся основные сведения о веб-приложении MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5ac81-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="5ac81-190">Это приложение служит для управления базой данных названий фильмов.</span><span class="sxs-lookup"><span data-stu-id="5ac81-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="5ac81-191">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="5ac81-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5ac81-192">Создание веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="5ac81-192">Create a web app.</span></span>
> * <span data-ttu-id="5ac81-193">Добавление модели и формирование шаблона.</span><span class="sxs-lookup"><span data-stu-id="5ac81-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="5ac81-194">Работа с базой данных.</span><span class="sxs-lookup"><span data-stu-id="5ac81-194">Work with a database.</span></span>
> * <span data-ttu-id="5ac81-195">Добавление поиска и проверки.</span><span class="sxs-lookup"><span data-stu-id="5ac81-195">Add search and validation.</span></span>

<span data-ttu-id="5ac81-196">В конечном итоге вы получите приложение, позволяющее управлять данными фильмов и отображать их.</span><span class="sxs-lookup"><span data-stu-id="5ac81-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="5ac81-197">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="5ac81-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5ac81-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ac81-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5ac81-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5ac81-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5ac81-200">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5ac81-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="5ac81-201">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="5ac81-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5ac81-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ac81-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5ac81-203">В Visual Studio выберите **Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="5ac81-204">Выберите пункт **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Новое веб-приложение ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="5ac81-206">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="5ac81-207">Имя **MvcMovie** необходимо присвоить для того, чтобы при копировании кода пространства имен совпали.</span><span class="sxs-lookup"><span data-stu-id="5ac81-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Новое веб-приложение ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="5ac81-209">Выберите **Веб-приложение (модель-представление-контроллер)** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-209">Select **Web Application(Model-View-Controller)** , and then select **Create**.</span></span>

![<span data-ttu-id="5ac81-210">Диалоговое окно нового проекта, .NET Core в левой области, веб-узел ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5ac81-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="5ac81-211">В Visual Studio используется только что созданный вами шаблон по умолчанию для проекта MVC.</span><span class="sxs-lookup"><span data-stu-id="5ac81-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="5ac81-212">Чтобы приложение стало рабочим, осталось только указать имя проекта и выбрать несколько параметров.</span><span class="sxs-lookup"><span data-stu-id="5ac81-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="5ac81-213">Это простой начальный проект и хорошая стартовая точка.</span><span class="sxs-lookup"><span data-stu-id="5ac81-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5ac81-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5ac81-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5ac81-215">Для работы с этим руководством требуется знание VS Code.</span><span class="sxs-lookup"><span data-stu-id="5ac81-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="5ac81-216">Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="5ac81-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="5ac81-217">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5ac81-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5ac81-218">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="5ac81-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="5ac81-219">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="5ac81-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="5ac81-220">Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="5ac81-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="5ac81-221">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-221">Select **Yes**</span></span>

  * <span data-ttu-id="5ac81-222">`dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="5ac81-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="5ac81-223">`code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5ac81-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5ac81-224">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5ac81-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5ac81-225">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-225">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="5ac81-227">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="5ac81-228">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="5ac81-229">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="5ac81-229">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="5ac81-230">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-230">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="5ac81-231">Если отобразится запрос на выбор **целевой платформы** , выберите последнюю версию 2.x.</span><span class="sxs-lookup"><span data-stu-id="5ac81-231">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="5ac81-232">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-232">Select **Next**.</span></span>

* <span data-ttu-id="5ac81-233">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-233">Name the project **MvcMovie** , and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="5ac81-234">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="5ac81-234">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5ac81-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ac81-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5ac81-236">Нажмите **CTRl+F5** , чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="5ac81-236">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="5ac81-237">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="5ac81-237">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5ac81-238">Обратите внимание на то, что в адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5ac81-238">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5ac81-239">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5ac81-239">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5ac81-240">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="5ac81-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="5ac81-241">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="5ac81-241">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5ac81-242">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="5ac81-242">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="5ac81-243">Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.</span><span class="sxs-lookup"><span data-stu-id="5ac81-243">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Меню отладки](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="5ac81-245">Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="5ac81-245">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="5ac81-247">Нажмите **Принять** , чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="5ac81-247">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5ac81-248">Это приложение не отслеживает персональные данные.</span><span class="sxs-lookup"><span data-stu-id="5ac81-248">This app doesn't track personal information.</span></span> <span data-ttu-id="5ac81-249">Созданный шаблоном код включает ресурсы для соблюдения [Общего регламента по защите данных (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5ac81-249">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/privacy.png)

  <span data-ttu-id="5ac81-251">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="5ac81-251">The following image shows the app after accepting tracking:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5ac81-253">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5ac81-253">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5ac81-254">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="5ac81-254">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="5ac81-255">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="5ac81-255">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="5ac81-256">В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5ac81-256">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="5ac81-257">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5ac81-257">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="5ac81-258">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5ac81-258">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="5ac81-259">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="5ac81-259">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5ac81-260">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро обновлять страницу и просматривать изменения.</span><span class="sxs-lookup"><span data-stu-id="5ac81-260">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="5ac81-261">Нажмите **Принять** , чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="5ac81-261">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5ac81-262">Это приложение не отслеживает персональные данные.</span><span class="sxs-lookup"><span data-stu-id="5ac81-262">This app doesn't track personal information.</span></span> <span data-ttu-id="5ac81-263">Созданный шаблоном код включает ресурсы для соблюдения [Общего регламента по защите данных (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5ac81-263">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/privacy.png)

  <span data-ttu-id="5ac81-265">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="5ac81-265">The following image shows the app after accepting tracking:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5ac81-267">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5ac81-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5ac81-268">Выберите **Выполнить** > **Запуск без отладки** , чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="5ac81-268">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="5ac81-269">Visual Studio для Mac запустит сервер [Kestrel](xref:fundamentals/servers/index#kestrel), откроет браузер и перейдет к `http://localhost:port`, где *port*  — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="5ac81-269">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="5ac81-270">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5ac81-270">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5ac81-271">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5ac81-271">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5ac81-272">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="5ac81-272">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5ac81-273">При запуске приложения вы увидите другой номер порта.</span><span class="sxs-lookup"><span data-stu-id="5ac81-273">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5ac81-274">В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.</span><span class="sxs-lookup"><span data-stu-id="5ac81-274">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="5ac81-275">Нажмите **Принять** , чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="5ac81-275">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5ac81-276">Это приложение не отслеживает персональные данные.</span><span class="sxs-lookup"><span data-stu-id="5ac81-276">This app doesn't track personal information.</span></span> <span data-ttu-id="5ac81-277">Созданный шаблоном код включает ресурсы для соблюдения [Общего регламента по защите данных (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5ac81-277">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="5ac81-279">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="5ac81-279">The following image shows the app after accepting tracking:</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="5ac81-281">В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.</span><span class="sxs-lookup"><span data-stu-id="5ac81-281">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5ac81-282">Вперед</span><span class="sxs-lookup"><span data-stu-id="5ac81-282">Next</span></span>](adding-controller.md)

::: moniker-end
