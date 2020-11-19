---
title: Начало работы с MVC ASP.NET Core
author: rick-anderson
description: Сведения о начале работы с MVC ASP.NET Core.
ms.author: riande
ms.date: 11/16/2020
no-loc:
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c96e7107c85bf36f55f6571c71c20d09bc94ddb3
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688531"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="5a686-103">Начало работы с MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a686-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="5a686-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="5a686-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="5a686-105">В этом учебнике приводятся основные сведения о веб-приложении MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5a686-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="5a686-106">Это приложение служит для управления базой данных названий фильмов.</span><span class="sxs-lookup"><span data-stu-id="5a686-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="5a686-107">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="5a686-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5a686-108">Создание веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="5a686-108">Create a web app.</span></span>
> * <span data-ttu-id="5a686-109">Добавление модели и формирование шаблона.</span><span class="sxs-lookup"><span data-stu-id="5a686-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="5a686-110">Работа с базой данных.</span><span class="sxs-lookup"><span data-stu-id="5a686-110">Work with a database.</span></span>
> * <span data-ttu-id="5a686-111">Добавление поиска и проверки.</span><span class="sxs-lookup"><span data-stu-id="5a686-111">Add search and validation.</span></span>

<span data-ttu-id="5a686-112">В конечном итоге вы получите приложение, позволяющее управлять данными фильмов и отображать их.</span><span class="sxs-lookup"><span data-stu-id="5a686-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="5a686-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="5a686-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a686-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a686-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a686-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a686-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5a686-116">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5a686-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="5a686-117">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="5a686-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a686-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a686-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5a686-119">Запустите Visual Studio и щелкните **Создать проект**</span><span class="sxs-lookup"><span data-stu-id="5a686-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="5a686-120">В диалоговом окне **Создать проект** выберите **Веб-приложение ASP.NET Core** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5a686-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="5a686-121">В диалоговом окне **Настроить новый проект** введите `MvcMovie` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="5a686-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="5a686-122">Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен (`namespace`) совпадали при копировании кода.</span><span class="sxs-lookup"><span data-stu-id="5a686-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="5a686-123">Щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5a686-123">Select **Create**.</span></span>
1. <span data-ttu-id="5a686-124">В диалоговом окне **Создайте веб-приложение ASP.NET Core** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="5a686-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="5a686-125">В раскрывающихся списках выберите **.NET Core** и **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="5a686-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="5a686-126">Щелкните **ASP.NET Core Web App (Model-View-Controller)** (Веб-приложение ASP.NET Core (модель — представление — контроллер)).</span><span class="sxs-lookup"><span data-stu-id="5a686-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="5a686-127">**Создание**</span><span class="sxs-lookup"><span data-stu-id="5a686-127">**Create**</span></span>

![<span data-ttu-id="5a686-128">Создание веб-приложения ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a686-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="5a686-129">Альтернативные подходы к созданию проекта см. в статье [Создание проекта в Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="5a686-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="5a686-130">В Visual Studio используется только что созданный вами шаблон по умолчанию для проекта MVC.</span><span class="sxs-lookup"><span data-stu-id="5a686-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="5a686-131">Чтобы приложение стало рабочим, осталось только указать имя проекта и выбрать несколько параметров.</span><span class="sxs-lookup"><span data-stu-id="5a686-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="5a686-132">Это простой начальный проект.</span><span class="sxs-lookup"><span data-stu-id="5a686-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a686-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a686-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a686-134">В этом руководстве предполагается, что вы умеете работать с VS Code.</span><span class="sxs-lookup"><span data-stu-id="5a686-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="5a686-135">Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="5a686-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="5a686-136">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5a686-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5a686-137">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="5a686-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="5a686-138">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="5a686-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="5a686-139">Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="5a686-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="5a686-140">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="5a686-140">Select **Yes**</span></span>

  * <span data-ttu-id="5a686-141">`dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="5a686-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="5a686-142">`code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5a686-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5a686-143">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5a686-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5a686-144">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="5a686-144">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="5a686-146">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5a686-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="5a686-147">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5a686-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Выбор шаблона веб-приложения macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="5a686-149">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="5a686-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="5a686-150">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="5a686-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="5a686-151">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 5.x.</span><span class="sxs-lookup"><span data-stu-id="5a686-151">If presented an option to select a **Target Framework**, select the latest 5.x version.</span></span>

  <span data-ttu-id="5a686-152">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5a686-152">Select **Next**.</span></span>

* <span data-ttu-id="5a686-153">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5a686-153">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Имя проекта macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="5a686-155">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="5a686-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a686-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a686-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5a686-157">Нажмите **CTRl+F5**, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="5a686-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="5a686-158">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="5a686-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5a686-159">Обратите внимание на то, что в адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a686-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a686-160">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5a686-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5a686-161">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="5a686-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="5a686-162">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="5a686-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5a686-163">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="5a686-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="5a686-164">Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.</span><span class="sxs-lookup"><span data-stu-id="5a686-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Меню отладки](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="5a686-166">Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="5a686-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="5a686-168">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="5a686-168">The following image shows the app:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a686-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a686-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a686-171">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="5a686-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="5a686-172">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="5a686-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="5a686-173">В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a686-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="5a686-174">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5a686-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="5a686-175">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5a686-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="5a686-176">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="5a686-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5a686-177">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро обновлять страницу и просматривать изменения.</span><span class="sxs-lookup"><span data-stu-id="5a686-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5a686-179">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5a686-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5a686-180">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="5a686-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="5a686-181">Visual Studio для Mac запустит сервер [Kestrel](xref:fundamentals/servers/index#kestrel), откроет браузер и перейдет к `http://localhost:port`, где *port* — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="5a686-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="5a686-182">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a686-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a686-183">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5a686-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5a686-184">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="5a686-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5a686-185">При запуске приложения вы увидите другой номер порта.</span><span class="sxs-lookup"><span data-stu-id="5a686-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5a686-186">В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.</span><span class="sxs-lookup"><span data-stu-id="5a686-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="5a686-187">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="5a686-187">The following image shows the app:</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="5a686-189">В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.</span><span class="sxs-lookup"><span data-stu-id="5a686-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5a686-190">Вперед</span><span class="sxs-lookup"><span data-stu-id="5a686-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="5a686-191">В этом учебнике приводятся основные сведения о веб-приложении MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5a686-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="5a686-192">Это приложение служит для управления базой данных названий фильмов.</span><span class="sxs-lookup"><span data-stu-id="5a686-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="5a686-193">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="5a686-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5a686-194">Создание веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="5a686-194">Create a web app.</span></span>
> * <span data-ttu-id="5a686-195">Добавление модели и формирование шаблона.</span><span class="sxs-lookup"><span data-stu-id="5a686-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="5a686-196">Работа с базой данных.</span><span class="sxs-lookup"><span data-stu-id="5a686-196">Work with a database.</span></span>
> * <span data-ttu-id="5a686-197">Добавление поиска и проверки.</span><span class="sxs-lookup"><span data-stu-id="5a686-197">Add search and validation.</span></span>

<span data-ttu-id="5a686-198">В конечном итоге вы получите приложение, позволяющее управлять данными фильмов и отображать их.</span><span class="sxs-lookup"><span data-stu-id="5a686-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="5a686-199">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="5a686-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a686-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a686-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a686-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a686-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5a686-202">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5a686-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="5a686-203">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="5a686-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a686-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a686-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a686-205">В Visual Studio выберите **Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="5a686-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="5a686-206">Выберите **Веб-приложение ASP.NET Core** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5a686-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![Новое веб-приложение ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="5a686-208">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5a686-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="5a686-209">Имя **MvcMovie** необходимо присвоить для того, чтобы при копировании кода пространства имен совпали.</span><span class="sxs-lookup"><span data-stu-id="5a686-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Новое веб-приложение ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="5a686-211">Выберите **Веб-приложение (модель — представление — контроллер)** .</span><span class="sxs-lookup"><span data-stu-id="5a686-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="5a686-212">В раскрывающихся списках выберите пункты **.NET Core** и **ASP.NET Core 3.1**, а затем щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5a686-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="5a686-213">Диалоговое окно нового проекта, .NET Core в левой области, веб-узел ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a686-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="5a686-214">В Visual Studio используется только что созданный вами шаблон по умолчанию для проекта MVC.</span><span class="sxs-lookup"><span data-stu-id="5a686-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="5a686-215">Чтобы приложение стало рабочим, осталось только указать имя проекта и выбрать несколько параметров.</span><span class="sxs-lookup"><span data-stu-id="5a686-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="5a686-216">Это простой начальный проект.</span><span class="sxs-lookup"><span data-stu-id="5a686-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a686-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a686-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a686-218">Для работы с этим руководством требуется знание VS Code.</span><span class="sxs-lookup"><span data-stu-id="5a686-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="5a686-219">Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="5a686-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="5a686-220">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5a686-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5a686-221">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="5a686-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="5a686-222">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="5a686-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="5a686-223">Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="5a686-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="5a686-224">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="5a686-224">Select **Yes**</span></span>

  * <span data-ttu-id="5a686-225">`dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="5a686-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="5a686-226">`code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5a686-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5a686-227">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5a686-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5a686-228">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="5a686-228">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="5a686-230">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5a686-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="5a686-231">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5a686-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Выбор шаблона веб-приложения macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="5a686-233">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="5a686-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="5a686-234">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="5a686-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="5a686-235">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 3.x.</span><span class="sxs-lookup"><span data-stu-id="5a686-235">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="5a686-236">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5a686-236">Select **Next**.</span></span>

* <span data-ttu-id="5a686-237">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5a686-237">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Имя проекта macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="5a686-239">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="5a686-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a686-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a686-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5a686-241">Нажмите **CTRl+F5**, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="5a686-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="5a686-242">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="5a686-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5a686-243">Обратите внимание на то, что в адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a686-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a686-244">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5a686-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5a686-245">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="5a686-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="5a686-246">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="5a686-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5a686-247">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="5a686-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="5a686-248">Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.</span><span class="sxs-lookup"><span data-stu-id="5a686-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Меню отладки](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="5a686-250">Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="5a686-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="5a686-252">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="5a686-252">The following image shows the app:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a686-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a686-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a686-255">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="5a686-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="5a686-256">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="5a686-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="5a686-257">В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a686-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="5a686-258">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5a686-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="5a686-259">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5a686-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="5a686-260">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="5a686-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5a686-261">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро обновлять страницу и просматривать изменения.</span><span class="sxs-lookup"><span data-stu-id="5a686-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5a686-263">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5a686-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5a686-264">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="5a686-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="5a686-265">Visual Studio для Mac запустит сервер [Kestrel](xref:fundamentals/servers/index#kestrel), откроет браузер и перейдет к `http://localhost:port`, где *port* — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="5a686-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="5a686-266">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a686-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a686-267">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5a686-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5a686-268">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="5a686-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5a686-269">При запуске приложения вы увидите другой номер порта.</span><span class="sxs-lookup"><span data-stu-id="5a686-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5a686-270">В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.</span><span class="sxs-lookup"><span data-stu-id="5a686-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="5a686-271">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="5a686-271">The following image shows the app:</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="5a686-273">В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.</span><span class="sxs-lookup"><span data-stu-id="5a686-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5a686-274">Вперед</span><span class="sxs-lookup"><span data-stu-id="5a686-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="5a686-275">В этом учебнике приводятся основные сведения о веб-приложении MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5a686-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="5a686-276">Это приложение служит для управления базой данных названий фильмов.</span><span class="sxs-lookup"><span data-stu-id="5a686-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="5a686-277">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="5a686-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5a686-278">Создание веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="5a686-278">Create a web app.</span></span>
> * <span data-ttu-id="5a686-279">Добавление модели и формирование шаблона.</span><span class="sxs-lookup"><span data-stu-id="5a686-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="5a686-280">Работа с базой данных.</span><span class="sxs-lookup"><span data-stu-id="5a686-280">Work with a database.</span></span>
> * <span data-ttu-id="5a686-281">Добавление поиска и проверки.</span><span class="sxs-lookup"><span data-stu-id="5a686-281">Add search and validation.</span></span>

<span data-ttu-id="5a686-282">В конечном итоге вы получите приложение, позволяющее управлять данными фильмов и отображать их.</span><span class="sxs-lookup"><span data-stu-id="5a686-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="5a686-283">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="5a686-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a686-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a686-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a686-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a686-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5a686-286">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5a686-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="5a686-287">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="5a686-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a686-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a686-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a686-289">В Visual Studio выберите **Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="5a686-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="5a686-290">Выберите пункт **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5a686-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Новое веб-приложение ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="5a686-292">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5a686-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="5a686-293">Имя **MvcMovie** необходимо присвоить для того, чтобы при копировании кода пространства имен совпали.</span><span class="sxs-lookup"><span data-stu-id="5a686-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Новое веб-приложение ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="5a686-295">Выберите **Веб-приложение (модель-представление-контроллер)** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5a686-295">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="5a686-296">Диалоговое окно нового проекта, .NET Core в левой области, веб-узел ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a686-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="5a686-297">В Visual Studio используется только что созданный вами шаблон по умолчанию для проекта MVC.</span><span class="sxs-lookup"><span data-stu-id="5a686-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="5a686-298">Чтобы приложение стало рабочим, осталось только указать имя проекта и выбрать несколько параметров.</span><span class="sxs-lookup"><span data-stu-id="5a686-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="5a686-299">Это простой начальный проект и хорошая стартовая точка.</span><span class="sxs-lookup"><span data-stu-id="5a686-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a686-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a686-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a686-301">Для работы с этим руководством требуется знание VS Code.</span><span class="sxs-lookup"><span data-stu-id="5a686-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="5a686-302">Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="5a686-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="5a686-303">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5a686-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5a686-304">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="5a686-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="5a686-305">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="5a686-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="5a686-306">Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="5a686-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="5a686-307">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="5a686-307">Select **Yes**</span></span>

  * <span data-ttu-id="5a686-308">`dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="5a686-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="5a686-309">`code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5a686-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5a686-310">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5a686-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5a686-311">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="5a686-311">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="5a686-313">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5a686-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="5a686-314">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5a686-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="5a686-315">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="5a686-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="5a686-316">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="5a686-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="5a686-317">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 2.x.</span><span class="sxs-lookup"><span data-stu-id="5a686-317">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="5a686-318">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="5a686-318">Select **Next**.</span></span>

* <span data-ttu-id="5a686-319">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="5a686-319">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="5a686-320">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="5a686-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a686-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a686-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5a686-322">Нажмите **CTRl+F5**, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="5a686-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="5a686-323">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="5a686-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5a686-324">Обратите внимание на то, что в адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a686-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a686-325">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5a686-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5a686-326">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="5a686-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="5a686-327">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="5a686-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5a686-328">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро запустить приложение и просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="5a686-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="5a686-329">Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.</span><span class="sxs-lookup"><span data-stu-id="5a686-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Меню отладки](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="5a686-331">Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="5a686-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="5a686-333">Нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="5a686-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5a686-334">Это приложение не отслеживает персональные данные.</span><span class="sxs-lookup"><span data-stu-id="5a686-334">This app doesn't track personal information.</span></span> <span data-ttu-id="5a686-335">Созданный шаблоном код включает ресурсы для соблюдения [Общего регламента по защите данных (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5a686-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/privacy.png)

  <span data-ttu-id="5a686-337">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="5a686-337">The following image shows the app after accepting tracking:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a686-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a686-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a686-340">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="5a686-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="5a686-341">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="5a686-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="5a686-342">В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a686-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="5a686-343">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5a686-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="5a686-344">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5a686-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="5a686-345">Запуск приложения с помощью клавиш CTRL+F5 (режим без отладки) позволяет внести изменения в код, сохранить файл, обновить браузер и увидеть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="5a686-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5a686-346">Многие разработчики предпочитают использовать режим без отладки, чтобы быстро обновлять страницу и просматривать изменения.</span><span class="sxs-lookup"><span data-stu-id="5a686-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="5a686-347">Нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="5a686-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5a686-348">Это приложение не отслеживает персональные данные.</span><span class="sxs-lookup"><span data-stu-id="5a686-348">This app doesn't track personal information.</span></span> <span data-ttu-id="5a686-349">Созданный шаблоном код включает ресурсы для соблюдения [Общего регламента по защите данных (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5a686-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/privacy.png)

  <span data-ttu-id="5a686-351">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="5a686-351">The following image shows the app after accepting tracking:</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5a686-353">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="5a686-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5a686-354">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="5a686-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="5a686-355">Visual Studio для Mac запустит сервер [Kestrel](xref:fundamentals/servers/index#kestrel), откроет браузер и перейдет к `http://localhost:port`, где *port* — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="5a686-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="5a686-356">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a686-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a686-357">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="5a686-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5a686-358">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="5a686-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5a686-359">При запуске приложения вы увидите другой номер порта.</span><span class="sxs-lookup"><span data-stu-id="5a686-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5a686-360">В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.</span><span class="sxs-lookup"><span data-stu-id="5a686-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="5a686-361">Нажмите **Принять**, чтобы согласиться на отслеживание.</span><span class="sxs-lookup"><span data-stu-id="5a686-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5a686-362">Это приложение не отслеживает персональные данные.</span><span class="sxs-lookup"><span data-stu-id="5a686-362">This app doesn't track personal information.</span></span> <span data-ttu-id="5a686-363">Созданный шаблоном код включает ресурсы для соблюдения [Общего регламента по защите данных (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5a686-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="5a686-365">На следующем рисунке показано приложение после принятия отслеживания:</span><span class="sxs-lookup"><span data-stu-id="5a686-365">The following image shows the app after accepting tracking:</span></span>

  ![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="5a686-367">В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.</span><span class="sxs-lookup"><span data-stu-id="5a686-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5a686-368">Вперед</span><span class="sxs-lookup"><span data-stu-id="5a686-368">Next</span></span>](adding-controller.md)

::: moniker-end
