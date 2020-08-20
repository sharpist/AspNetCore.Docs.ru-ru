---
title: Начало работы с MVC ASP.NET Core
author: rick-anderson
description: Сведения о начале работы с MVC ASP.NET Core.
ms.author: riande
ms.date: 10/16/2019
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 9d70b292a93a5d19cc25b2fc592ec88ce8262434
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629994"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="66f25-103">Начало работы с MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66f25-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="66f25-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="66f25-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="66f25-105">В этом учебнике приводятся основные сведения о веб-приложении MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="66f25-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="66f25-106">Это приложение служит для управления базой данных названий фильмов.</span><span class="sxs-lookup"><span data-stu-id="66f25-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="66f25-107">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="66f25-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="66f25-108">Создание веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="66f25-108">Create a web app.</span></span>
> * <span data-ttu-id="66f25-109">Добавление модели и формирование шаблона.</span><span class="sxs-lookup"><span data-stu-id="66f25-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="66f25-110">Работа с базой данных.</span><span class="sxs-lookup"><span data-stu-id="66f25-110">Work with a database.</span></span>
> * <span data-ttu-id="66f25-111">Добавление поиска и проверки.</span><span class="sxs-lookup"><span data-stu-id="66f25-111">Add search and validation.</span></span>

<span data-ttu-id="66f25-112">В конечном итоге вы получите приложение, позволяющее управлять данными фильмов и отображать их.</span><span class="sxs-lookup"><span data-stu-id="66f25-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="66f25-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="66f25-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66f25-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66f25-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="66f25-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66f25-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66f25-116">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="66f25-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="66f25-117">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="66f25-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66f25-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66f25-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="66f25-119">В Visual Studio выберите **Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="66f25-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="66f25-120">Выберите пункт **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="66f25-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Новое веб-приложение ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="66f25-122">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="66f25-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="66f25-123">Имя **MvcMovie** необходимо присвоить для того, чтобы при копировании кода пространства имен совпали.</span><span class="sxs-lookup"><span data-stu-id="66f25-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Новое веб-приложение ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="66f25-125">Выберите **Веб-приложение (модель-представление-контроллер)** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="66f25-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="66f25-126">Диалоговое окно нового проекта, .NET Core в левой области, веб-узел ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66f25-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="66f25-127">В Visual Studio используется только что созданный вами шаблон по умолчанию для проекта MVC.</span><span class="sxs-lookup"><span data-stu-id="66f25-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="66f25-128">Чтобы приложение стало рабочим, осталось только указать имя проекта и выбрать несколько параметров.</span><span class="sxs-lookup"><span data-stu-id="66f25-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="66f25-129">Это простой начальный проект.</span><span class="sxs-lookup"><span data-stu-id="66f25-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66f25-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66f25-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="66f25-131">Для работы с этим руководством требуется знание VS Code.</span><span class="sxs-lookup"><span data-stu-id="66f25-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="66f25-132">Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="66f25-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="66f25-133">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="66f25-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="66f25-134">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="66f25-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="66f25-135">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="66f25-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="66f25-136">Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="66f25-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="66f25-137">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="66f25-137">Select **Yes**</span></span>

  * <span data-ttu-id="66f25-138">`dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="66f25-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="66f25-139">`code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="66f25-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66f25-140">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="66f25-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="66f25-141">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="66f25-141">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="66f25-143">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="66f25-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="66f25-144">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="66f25-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Выбор шаблона веб-приложения macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="66f25-146">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="66f25-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="66f25-147">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="66f25-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="66f25-148">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 3.x.</span><span class="sxs-lookup"><span data-stu-id="66f25-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="66f25-149">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="66f25-149">Select **Next**.</span></span>

* <span data-ttu-id="66f25-150">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="66f25-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Имя проекта macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="66f25-152">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="66f25-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66f25-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66f25-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66f25-154">Нажмите **CTRl+F5**, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="66f25-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="66f25-155">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="66f25-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="66f25-156">Обратите внимание на то, что в адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="66f25-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="66f25-157">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="66f25-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="66f25-158">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="66f25-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="66f25-159">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="66f25-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="66f25-160">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="66f25-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="66f25-161">Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.</span><span class="sxs-lookup"><span data-stu-id="66f25-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Меню отладки](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="66f25-163">Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="66f25-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="66f25-165">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="66f25-165">The following image shows the app:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="66f25-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66f25-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="66f25-168">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="66f25-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="66f25-169">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="66f25-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="66f25-170">В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="66f25-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="66f25-171">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="66f25-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="66f25-172">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="66f25-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="66f25-173">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="66f25-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="66f25-174">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро обновлять страницу и просматривать изменения.</span><span class="sxs-lookup"><span data-stu-id="66f25-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66f25-176">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="66f25-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="66f25-177">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="66f25-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="66f25-178">Visual Studio для Mac запустит сервер [Kestrel](xref:fundamentals/servers/index#kestrel), откроет браузер и перейдет к `http://localhost:port`, где *port* — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="66f25-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="66f25-179">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="66f25-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="66f25-180">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="66f25-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="66f25-181">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="66f25-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="66f25-182">При запуске приложения вы увидите другой номер порта.</span><span class="sxs-lookup"><span data-stu-id="66f25-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="66f25-183">В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.</span><span class="sxs-lookup"><span data-stu-id="66f25-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="66f25-184">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="66f25-184">The following image shows the app:</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="66f25-186">В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.</span><span class="sxs-lookup"><span data-stu-id="66f25-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="66f25-187">Вперед</span><span class="sxs-lookup"><span data-stu-id="66f25-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="66f25-188">В этом учебнике приводятся основные сведения о веб-приложении MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="66f25-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="66f25-189">Это приложение служит для управления базой данных названий фильмов.</span><span class="sxs-lookup"><span data-stu-id="66f25-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="66f25-190">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="66f25-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="66f25-191">Создание веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="66f25-191">Create a web app.</span></span>
> * <span data-ttu-id="66f25-192">Добавление модели и формирование шаблона.</span><span class="sxs-lookup"><span data-stu-id="66f25-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="66f25-193">Работа с базой данных.</span><span class="sxs-lookup"><span data-stu-id="66f25-193">Work with a database.</span></span>
> * <span data-ttu-id="66f25-194">Добавление поиска и проверки.</span><span class="sxs-lookup"><span data-stu-id="66f25-194">Add search and validation.</span></span>

<span data-ttu-id="66f25-195">В конечном итоге вы получите приложение, позволяющее управлять данными фильмов и отображать их.</span><span class="sxs-lookup"><span data-stu-id="66f25-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="66f25-196">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="66f25-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66f25-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66f25-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="66f25-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66f25-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66f25-199">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="66f25-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="66f25-200">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="66f25-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66f25-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66f25-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="66f25-202">В Visual Studio выберите **Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="66f25-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="66f25-203">Выберите пункт **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="66f25-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Новое веб-приложение ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="66f25-205">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="66f25-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="66f25-206">Имя **MvcMovie** необходимо присвоить для того, чтобы при копировании кода пространства имен совпали.</span><span class="sxs-lookup"><span data-stu-id="66f25-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Новое веб-приложение ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="66f25-208">Выберите **Веб-приложение (модель-представление-контроллер)** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="66f25-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="66f25-209">Диалоговое окно нового проекта, .NET Core в левой области, веб-узел ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66f25-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="66f25-210">В Visual Studio используется только что созданный вами шаблон по умолчанию для проекта MVC.</span><span class="sxs-lookup"><span data-stu-id="66f25-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="66f25-211">Чтобы приложение стало рабочим, осталось только указать имя проекта и выбрать несколько параметров.</span><span class="sxs-lookup"><span data-stu-id="66f25-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="66f25-212">Это простой начальный проект и хорошая стартовая точка.</span><span class="sxs-lookup"><span data-stu-id="66f25-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66f25-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66f25-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="66f25-214">Для работы с этим руководством требуется знание VS Code.</span><span class="sxs-lookup"><span data-stu-id="66f25-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="66f25-215">Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="66f25-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="66f25-216">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="66f25-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="66f25-217">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="66f25-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="66f25-218">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="66f25-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="66f25-219">Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="66f25-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="66f25-220">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="66f25-220">Select **Yes**</span></span>

  * <span data-ttu-id="66f25-221">`dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="66f25-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="66f25-222">`code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="66f25-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66f25-223">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="66f25-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="66f25-224">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="66f25-224">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="66f25-226">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="66f25-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="66f25-227">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="66f25-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="66f25-228">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="66f25-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="66f25-229">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="66f25-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="66f25-230">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 2.x.</span><span class="sxs-lookup"><span data-stu-id="66f25-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="66f25-231">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="66f25-231">Select **Next**.</span></span>

* <span data-ttu-id="66f25-232">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="66f25-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="66f25-233">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="66f25-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66f25-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66f25-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66f25-235">Нажмите **CTRl+F5**, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="66f25-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="66f25-236">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="66f25-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="66f25-237">Обратите внимание на то, что в адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="66f25-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="66f25-238">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="66f25-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="66f25-239">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="66f25-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="66f25-240">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="66f25-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="66f25-241">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="66f25-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="66f25-242">Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.</span><span class="sxs-lookup"><span data-stu-id="66f25-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Меню отладки](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="66f25-244">Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="66f25-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="66f25-246">Нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="66f25-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="66f25-247">Это приложение не отслеживает персональные данные.</span><span class="sxs-lookup"><span data-stu-id="66f25-247">This app doesn't track personal information.</span></span> <span data-ttu-id="66f25-248">Созданный шаблоном код включает ресурсы для соблюдения [Общего регламента по защите данных (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="66f25-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/privacy.png)

  <span data-ttu-id="66f25-250">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="66f25-250">The following image shows the app after accepting tracking:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="66f25-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66f25-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="66f25-253">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="66f25-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="66f25-254">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="66f25-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="66f25-255">В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="66f25-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="66f25-256">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="66f25-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="66f25-257">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="66f25-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="66f25-258">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="66f25-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="66f25-259">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро обновлять страницу и просматривать изменения.</span><span class="sxs-lookup"><span data-stu-id="66f25-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="66f25-260">Нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="66f25-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="66f25-261">Это приложение не отслеживает персональные данные.</span><span class="sxs-lookup"><span data-stu-id="66f25-261">This app doesn't track personal information.</span></span> <span data-ttu-id="66f25-262">Созданный шаблоном код включает ресурсы для соблюдения [Общего регламента по защите данных (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="66f25-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/privacy.png)

  <span data-ttu-id="66f25-264">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="66f25-264">The following image shows the app after accepting tracking:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66f25-266">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="66f25-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="66f25-267">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="66f25-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="66f25-268">Visual Studio для Mac запустит сервер [Kestrel](xref:fundamentals/servers/index#kestrel), откроет браузер и перейдет к `http://localhost:port`, где *port* — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="66f25-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="66f25-269">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="66f25-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="66f25-270">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="66f25-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="66f25-271">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="66f25-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="66f25-272">При запуске приложения вы увидите другой номер порта.</span><span class="sxs-lookup"><span data-stu-id="66f25-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="66f25-273">В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.</span><span class="sxs-lookup"><span data-stu-id="66f25-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="66f25-274">Нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="66f25-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="66f25-275">Это приложение не отслеживает персональные данные.</span><span class="sxs-lookup"><span data-stu-id="66f25-275">This app doesn't track personal information.</span></span> <span data-ttu-id="66f25-276">Созданный шаблоном код включает ресурсы для соблюдения [Общего регламента по защите данных (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="66f25-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="66f25-278">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="66f25-278">The following image shows the app after accepting tracking:</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="66f25-280">В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.</span><span class="sxs-lookup"><span data-stu-id="66f25-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="66f25-281">Вперед</span><span class="sxs-lookup"><span data-stu-id="66f25-281">Next</span></span>](adding-controller.md)

::: moniker-end
