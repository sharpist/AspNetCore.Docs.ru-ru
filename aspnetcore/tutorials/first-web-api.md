---
title: Учебник. Создание веб-API с помощью ASP.NET Core
author: rick-anderson
description: Узнайте, как создать веб-API с помощью ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 7418e962076fae3ebdbb25381838757b09046578
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417664"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="168e8-103">Учебник. Создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="168e8-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="168e8-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Майк Уоссон](https://github.com/mikewasson) (Mike Wasson)</span><span class="sxs-lookup"><span data-stu-id="168e8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="168e8-105">В этом учебнике приводятся основные сведения о создании веб-API с помощью ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="168e8-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="168e8-106">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="168e8-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="168e8-107">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="168e8-107">Create a web API project.</span></span>
> * <span data-ttu-id="168e8-108">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="168e8-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="168e8-109">Формирование шаблонов контроллера с использованием методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="168e8-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="168e8-110">Настройка маршрутизации, URL-пути и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="168e8-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="168e8-111">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="168e8-111">Call the web API with Postman.</span></span>

<span data-ttu-id="168e8-112">В итоге вы получите веб-API, позволяющий работать с элементами списка дел, хранимыми в базе данных.</span><span class="sxs-lookup"><span data-stu-id="168e8-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="168e8-113">Обзор</span><span class="sxs-lookup"><span data-stu-id="168e8-113">Overview</span></span>

<span data-ttu-id="168e8-114">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="168e8-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="168e8-115">API</span><span class="sxs-lookup"><span data-stu-id="168e8-115">API</span></span> | <span data-ttu-id="168e8-116">Описание</span><span class="sxs-lookup"><span data-stu-id="168e8-116">Description</span></span> | <span data-ttu-id="168e8-117">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="168e8-117">Request body</span></span> | <span data-ttu-id="168e8-118">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="168e8-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="168e8-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="168e8-119">GET /api/TodoItems</span></span> | <span data-ttu-id="168e8-120">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="168e8-120">Get all to-do items</span></span> | <span data-ttu-id="168e8-121">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="168e8-121">None</span></span> | <span data-ttu-id="168e8-122">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="168e8-122">Array of to-do items</span></span>|
|<span data-ttu-id="168e8-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="168e8-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="168e8-124">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="168e8-124">Get an item by ID</span></span> | <span data-ttu-id="168e8-125">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="168e8-125">None</span></span> | <span data-ttu-id="168e8-126">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="168e8-126">To-do item</span></span>|
|<span data-ttu-id="168e8-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="168e8-127">POST /api/TodoItems</span></span> | <span data-ttu-id="168e8-128">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="168e8-128">Add a new item</span></span> | <span data-ttu-id="168e8-129">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="168e8-129">To-do item</span></span> | <span data-ttu-id="168e8-130">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="168e8-130">To-do item</span></span> |
|<span data-ttu-id="168e8-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="168e8-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="168e8-132">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="168e8-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="168e8-133">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="168e8-133">To-do item</span></span> | <span data-ttu-id="168e8-134">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="168e8-134">None</span></span> |
|<span data-ttu-id="168e8-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="168e8-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="168e8-136">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="168e8-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="168e8-137">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="168e8-137">None</span></span> | <span data-ttu-id="168e8-138">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="168e8-138">None</span></span>|

<span data-ttu-id="168e8-139">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="168e8-139">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="168e8-145">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="168e8-145">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="168e8-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168e8-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168e8-148">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="168e8-149">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="168e8-149">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="168e8-151">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="168e8-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="168e8-152">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="168e8-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="168e8-153">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="168e8-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="168e8-154">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="168e8-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="168e8-155">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="168e8-155">Select the **API** template and click **Create**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="168e8-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168e8-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="168e8-158">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="168e8-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="168e8-159">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="168e8-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="168e8-160">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="168e8-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="168e8-161">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="168e8-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="168e8-162">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="168e8-162">The preceding commands:</span></span>

  * <span data-ttu-id="168e8-163">Создает новый проект веб-API и открывает его в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="168e8-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="168e8-164">Добавляет пакеты NuGet, которые понадобятся в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="168e8-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168e8-165">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="168e8-166">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="168e8-166">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="168e8-168">Щелкните **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="168e8-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Диалоговое окно "Новый проект" в macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="168e8-170">В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **Целевой платформы** выберите \* *.NET Core 3.1*.</span><span class="sxs-lookup"><span data-stu-id="168e8-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="168e8-171">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="168e8-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="168e8-173">Откройте командный терминал в папке проекта и выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="168e8-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="168e8-174">Тестирование API</span><span class="sxs-lookup"><span data-stu-id="168e8-174">Test the API</span></span>

<span data-ttu-id="168e8-175">Шаблон проекта создает API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="168e8-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="168e8-176">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="168e8-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="168e8-178">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="168e8-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="168e8-179">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/WeatherForecast`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="168e8-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="168e8-180">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="168e8-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="168e8-181">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="168e8-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="168e8-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168e8-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="168e8-183">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="168e8-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="168e8-184">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="168e8-184">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168e8-185">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="168e8-186">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="168e8-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="168e8-187">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="168e8-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="168e8-188">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="168e8-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="168e8-189">Добавьте `/WeatherForecast` к URL-адресу (измените URL-адрес на `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="168e8-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="168e8-190">Возвращаемые данные JSON будут выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="168e8-190">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="168e8-191">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="168e8-191">Add a model class</span></span>

<span data-ttu-id="168e8-192">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="168e8-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="168e8-193">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="168e8-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="168e8-195">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="168e8-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="168e8-196">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="168e8-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="168e8-197">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="168e8-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="168e8-198">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="168e8-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="168e8-199">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="168e8-200">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="168e8-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="168e8-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168e8-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="168e8-202">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="168e8-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="168e8-203">Добавьте класс `TodoItem` в папку *Models*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="168e8-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168e8-204">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="168e8-205">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="168e8-205">Right-click the project.</span></span> <span data-ttu-id="168e8-206">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="168e8-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="168e8-207">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="168e8-207">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="168e8-209">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="168e8-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="168e8-210">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="168e8-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="168e8-211">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="168e8-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="168e8-212">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="168e8-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="168e8-213">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models*.</span><span class="sxs-lookup"><span data-stu-id="168e8-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="168e8-214">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="168e8-214">Add a database context</span></span>

<span data-ttu-id="168e8-215">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="168e8-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="168e8-216">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="168e8-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="168e8-218">Добавление Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="168e8-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="168e8-219">В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.</span><span class="sxs-lookup"><span data-stu-id="168e8-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="168e8-220">Перейдите на вкладку **Обзор** и введите **Microsoft.EntityFrameworkCore.SqlServer** в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="168e8-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="168e8-221">На панели слева выберите **Microsoft.EntityFrameworkCore.SqlServer**.</span><span class="sxs-lookup"><span data-stu-id="168e8-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="168e8-222">Установите флажок **Проект** на правой панели и выберите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="168e8-223">Добавьте пакет NuGet `Microsoft.EntityFrameworkCore.InMemory` согласно инструкциям выше.</span><span class="sxs-lookup"><span data-stu-id="168e8-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Диспетчер пакетов NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="168e8-225">Добавление контекста базы данных TodoContext</span><span class="sxs-lookup"><span data-stu-id="168e8-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="168e8-226">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="168e8-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="168e8-227">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="168e8-228">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="168e8-229">Добавьте класс `TodoContext` в папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="168e8-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="168e8-230">Введите следующий код:</span><span class="sxs-lookup"><span data-stu-id="168e8-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="168e8-231">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="168e8-231">Register the database context</span></span>

<span data-ttu-id="168e8-232">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="168e8-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="168e8-233">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="168e8-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="168e8-234">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="168e8-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="168e8-235">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="168e8-235">The preceding code:</span></span>

* <span data-ttu-id="168e8-236">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="168e8-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="168e8-237">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="168e8-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="168e8-238">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="168e8-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="168e8-239">Формирование шаблонов контроллера</span><span class="sxs-lookup"><span data-stu-id="168e8-239">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="168e8-241">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="168e8-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="168e8-242">Щелкните **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="168e8-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="168e8-243">Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="168e8-244">В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="168e8-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="168e8-245">Выберите **TodoItem (TodoApi.Models)** в поле **Класс модели**.</span><span class="sxs-lookup"><span data-stu-id="168e8-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="168e8-246">Выберите **TodoContext (TodoApi.Models)** в поле **Класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="168e8-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="168e8-247">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="168e8-248">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="168e8-249">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="168e8-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="168e8-250">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="168e8-250">The preceding commands:</span></span>

* <span data-ttu-id="168e8-251">добавляют пакеты NuGet, необходимые для формирования шаблонов;</span><span class="sxs-lookup"><span data-stu-id="168e8-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="168e8-252">устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);</span><span class="sxs-lookup"><span data-stu-id="168e8-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="168e8-253">формируют шаблоны для `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="168e8-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="168e8-254">Сформированный код:</span><span class="sxs-lookup"><span data-stu-id="168e8-254">The generated code:</span></span>

* <span data-ttu-id="168e8-255">Пометьте этот класс атрибутом [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="168e8-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="168e8-256">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="168e8-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="168e8-257">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="168e8-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="168e8-258">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="168e8-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="168e8-259">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="168e8-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="168e8-260">Шаблоны ASP.NET Core для:</span><span class="sxs-lookup"><span data-stu-id="168e8-260">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="168e8-261">Контроллеры с представлениями включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="168e8-261">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="168e8-262">Контроллеры API не включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="168e8-262">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="168e8-263">Если токен `[action]` не находится в шаблоне маршрута, имя [действия](xref:mvc/controllers/routing#action) исключается из маршрута.</span><span class="sxs-lookup"><span data-stu-id="168e8-263">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="168e8-264">То есть имя связанного метода действия не используется в соответствующем маршруте.</span><span class="sxs-lookup"><span data-stu-id="168e8-264">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="168e8-265">Знакомство с методом создания PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="168e8-265">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="168e8-266">Измените инструкцию возврата в `PostTodoItem` и используйте оператор [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="168e8-266">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="168e8-267">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="168e8-267">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="168e8-268">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="168e8-268">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="168e8-269">Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="168e8-269">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="168e8-270">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="168e8-270">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="168e8-271">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="168e8-271">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="168e8-272">Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location).</span><span class="sxs-lookup"><span data-stu-id="168e8-272">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="168e8-273">Заголовок `Location` указывает [URI](https://developer.mozilla.org/docs/Glossary/URI) новой созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="168e8-273">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="168e8-274">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="168e8-274">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="168e8-275">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="168e8-275">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="168e8-276">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="168e8-276">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="168e8-277">Установка Postman</span><span class="sxs-lookup"><span data-stu-id="168e8-277">Install Postman</span></span>

<span data-ttu-id="168e8-278">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="168e8-278">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="168e8-279">Установка [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="168e8-279">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="168e8-280">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="168e8-280">Start the web app.</span></span>
* <span data-ttu-id="168e8-281">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="168e8-281">Start Postman.</span></span>
* <span data-ttu-id="168e8-282">Отключение параметра **Проверка SSL-сертификата**</span><span class="sxs-lookup"><span data-stu-id="168e8-282">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="168e8-283">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="168e8-283">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="168e8-284">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="168e8-284">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="168e8-285">Тестирование PostTodoItem с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="168e8-285">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="168e8-286">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="168e8-286">Create a new request.</span></span>
* <span data-ttu-id="168e8-287">Установите HTTP-метод `POST`.</span><span class="sxs-lookup"><span data-stu-id="168e8-287">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="168e8-288">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="168e8-288">Select the **Body** tab.</span></span>
* <span data-ttu-id="168e8-289">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="168e8-289">Select the **raw** radio button.</span></span>
* <span data-ttu-id="168e8-290">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="168e8-290">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="168e8-291">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="168e8-291">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="168e8-292">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-292">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="168e8-294">Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="168e8-294">Test the location header URI</span></span>

* <span data-ttu-id="168e8-295">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="168e8-295">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="168e8-296">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="168e8-296">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="168e8-298">Укажите метод GET.</span><span class="sxs-lookup"><span data-stu-id="168e8-298">Set the method to GET.</span></span>
* <span data-ttu-id="168e8-299">Вставьте URI (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="168e8-299">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="168e8-300">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-300">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="168e8-301">Знакомство с методами GET</span><span class="sxs-lookup"><span data-stu-id="168e8-301">Examine the GET methods</span></span>

<span data-ttu-id="168e8-302">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="168e8-302">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="168e8-303">Протестируйте приложение, вызвав эти две конечные точки в браузере или в Postman.</span><span class="sxs-lookup"><span data-stu-id="168e8-303">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="168e8-304">Пример:</span><span class="sxs-lookup"><span data-stu-id="168e8-304">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="168e8-305">При вызове `GetTodoItems` возвращается примерно такой ответ:</span><span class="sxs-lookup"><span data-stu-id="168e8-305">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="168e8-306">Тестирование Get с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="168e8-306">Test Get with Postman</span></span>

* <span data-ttu-id="168e8-307">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="168e8-307">Create a new request.</span></span>
* <span data-ttu-id="168e8-308">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="168e8-308">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="168e8-309">Укажите URL-адрес запроса `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="168e8-309">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="168e8-310">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="168e8-310">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="168e8-311">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="168e8-311">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="168e8-312">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-312">Select **Send**.</span></span>

<span data-ttu-id="168e8-313">Это приложение использует выполняющуюся в памяти базу данных.</span><span class="sxs-lookup"><span data-stu-id="168e8-313">This app uses an in-memory database.</span></span> <span data-ttu-id="168e8-314">Если остановить и вновь запустить его, предшествующий запрос GET не возвратит никаких данных.</span><span class="sxs-lookup"><span data-stu-id="168e8-314">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="168e8-315">Если данные не возвращаются, данные для приложения получаются методом [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="168e8-315">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="168e8-316">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="168e8-316">Routing and URL paths</span></span>

<span data-ttu-id="168e8-317">Атрибут [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="168e8-317">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="168e8-318">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="168e8-318">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="168e8-319">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="168e8-319">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="168e8-320">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="168e8-320">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="168e8-321">В этом примере класс контроллера имеет имя **TodoItems**, а сам контроллер, соответственно, — "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="168e8-321">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="168e8-322">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="168e8-322">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="168e8-323">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="168e8-323">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="168e8-324">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="168e8-324">This sample doesn't use a template.</span></span> <span data-ttu-id="168e8-325">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="168e8-325">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="168e8-326">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="168e8-326">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="168e8-327">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="168e8-327">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="168e8-328">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="168e8-328">Return values</span></span>

<span data-ttu-id="168e8-329">Возвращаемое значение имеет тип `GetTodoItems`, а метод `GetTodoItem` имеет тип [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="168e8-329">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="168e8-330">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="168e8-330">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="168e8-331">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="168e8-331">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="168e8-332">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="168e8-332">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="168e8-333">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="168e8-333">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="168e8-334">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="168e8-334">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="168e8-335">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 ([Не найдено](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)).</span><span class="sxs-lookup"><span data-stu-id="168e8-335">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="168e8-336">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="168e8-336">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="168e8-337">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="168e8-337">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="168e8-338">Метод PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="168e8-338">The PutTodoItem method</span></span>

<span data-ttu-id="168e8-339">Проверьте метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="168e8-339">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="168e8-340">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="168e8-340">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="168e8-341">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="168e8-341">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="168e8-342">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="168e8-342">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="168e8-343">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="168e8-343">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="168e8-344">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="168e8-344">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="168e8-345">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="168e8-345">Test the PutTodoItem method</span></span>

<span data-ttu-id="168e8-346">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="168e8-346">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="168e8-347">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="168e8-347">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="168e8-348">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="168e8-348">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="168e8-349">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="168e8-349">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="168e8-350">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="168e8-350">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="168e8-352">Метод DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="168e8-352">The DeleteTodoItem method</span></span>

<span data-ttu-id="168e8-353">Проверьте метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="168e8-353">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="168e8-354">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="168e8-354">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="168e8-355">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="168e8-355">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="168e8-356">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="168e8-356">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="168e8-357">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="168e8-357">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="168e8-358">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-358">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="168e8-359">Предотвращение избыточной публикации</span><span class="sxs-lookup"><span data-stu-id="168e8-359">Prevent over-posting</span></span>

<span data-ttu-id="168e8-360">В настоящее время пример приложения предоставляет весь объект `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="168e8-360">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="168e8-361">Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели.</span><span class="sxs-lookup"><span data-stu-id="168e8-361">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="168e8-362">Это связано с несколькими причинами, и безопасность является основной.</span><span class="sxs-lookup"><span data-stu-id="168e8-362">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="168e8-363">Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления.</span><span class="sxs-lookup"><span data-stu-id="168e8-363">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="168e8-364">В этой статье используется **DTO**.</span><span class="sxs-lookup"><span data-stu-id="168e8-364">**DTO** is used in this article.</span></span>

<span data-ttu-id="168e8-365">DTO можно использовать для следующего:</span><span class="sxs-lookup"><span data-stu-id="168e8-365">A DTO may be used to:</span></span>

* <span data-ttu-id="168e8-366">Предотвращение избыточной публикации.</span><span class="sxs-lookup"><span data-stu-id="168e8-366">Prevent over-posting.</span></span>
* <span data-ttu-id="168e8-367">Скрытие свойств, которые не предназначены для просмотра клиентами.</span><span class="sxs-lookup"><span data-stu-id="168e8-367">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="168e8-368">Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="168e8-368">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="168e8-369">Сведение графов объектов, содержащих вложенные объекты.</span><span class="sxs-lookup"><span data-stu-id="168e8-369">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="168e8-370">Сведенные графы объектов могут быть удобнее для клиентов.</span><span class="sxs-lookup"><span data-stu-id="168e8-370">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="168e8-371">Чтобы продемонстрировать подход с применением DTO, обновите класс `TodoItem`, включив в него поле секрета:</span><span class="sxs-lookup"><span data-stu-id="168e8-371">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="168e8-372">Поле секрета должно быть скрыто в этом приложении, однако административное приложение может отобразить его.</span><span class="sxs-lookup"><span data-stu-id="168e8-372">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="168e8-373">Убедитесь, что вы можете отправить и получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="168e8-373">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="168e8-374">Создайте модель DTO:</span><span class="sxs-lookup"><span data-stu-id="168e8-374">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="168e8-375">Обновите `TodoItemsController` для использования `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="168e8-375">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="168e8-376">Убедитесь, что вы можете отправить или получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="168e8-376">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="168e8-377">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="168e8-377">Call the web API with JavaScript</span></span>

<span data-ttu-id="168e8-378">См. руководство по [: Вызовите веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="168e8-378">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="168e8-379">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="168e8-379">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="168e8-380">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="168e8-380">Create a web API project.</span></span>
> * <span data-ttu-id="168e8-381">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="168e8-381">Add a model class and a database context.</span></span>
> * <span data-ttu-id="168e8-382">Добавление контроллера.</span><span class="sxs-lookup"><span data-stu-id="168e8-382">Add a controller.</span></span>
> * <span data-ttu-id="168e8-383">Добавление методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="168e8-383">Add CRUD methods.</span></span>
> * <span data-ttu-id="168e8-384">Настройка маршрутизации и путей URL.</span><span class="sxs-lookup"><span data-stu-id="168e8-384">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="168e8-385">Указание возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="168e8-385">Specify return values.</span></span>
> * <span data-ttu-id="168e8-386">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="168e8-386">Call the web API with Postman.</span></span>
> * <span data-ttu-id="168e8-387">Вызовите веб-API с помощью JavaScript.</span><span class="sxs-lookup"><span data-stu-id="168e8-387">Call the web API with JavaScript.</span></span>

<span data-ttu-id="168e8-388">В конечном итоге вы получите веб-API, обеспечивающий управление элементами списка дел, хранящимися в реляционной базе данных.</span><span class="sxs-lookup"><span data-stu-id="168e8-388">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="168e8-389">Обзор</span><span class="sxs-lookup"><span data-stu-id="168e8-389">Overview</span></span>

<span data-ttu-id="168e8-390">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="168e8-390">This tutorial creates the following API:</span></span>

|<span data-ttu-id="168e8-391">API</span><span class="sxs-lookup"><span data-stu-id="168e8-391">API</span></span> | <span data-ttu-id="168e8-392">Описание</span><span class="sxs-lookup"><span data-stu-id="168e8-392">Description</span></span> | <span data-ttu-id="168e8-393">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="168e8-393">Request body</span></span> | <span data-ttu-id="168e8-394">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="168e8-394">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="168e8-395">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="168e8-395">GET /api/TodoItems</span></span> | <span data-ttu-id="168e8-396">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="168e8-396">Get all to-do items</span></span> | <span data-ttu-id="168e8-397">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="168e8-397">None</span></span> | <span data-ttu-id="168e8-398">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="168e8-398">Array of to-do items</span></span>|
|<span data-ttu-id="168e8-399">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="168e8-399">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="168e8-400">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="168e8-400">Get an item by ID</span></span> | <span data-ttu-id="168e8-401">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="168e8-401">None</span></span> | <span data-ttu-id="168e8-402">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="168e8-402">To-do item</span></span>|
|<span data-ttu-id="168e8-403">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="168e8-403">POST /api/TodoItems</span></span> | <span data-ttu-id="168e8-404">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="168e8-404">Add a new item</span></span> | <span data-ttu-id="168e8-405">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="168e8-405">To-do item</span></span> | <span data-ttu-id="168e8-406">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="168e8-406">To-do item</span></span> |
|<span data-ttu-id="168e8-407">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="168e8-407">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="168e8-408">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="168e8-408">Update an existing item &nbsp;</span></span> | <span data-ttu-id="168e8-409">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="168e8-409">To-do item</span></span> | <span data-ttu-id="168e8-410">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="168e8-410">None</span></span> |
|<span data-ttu-id="168e8-411">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="168e8-411">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="168e8-412">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="168e8-412">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="168e8-413">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="168e8-413">None</span></span> | <span data-ttu-id="168e8-414">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="168e8-414">None</span></span>|

<span data-ttu-id="168e8-415">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="168e8-415">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="168e8-421">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="168e8-421">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-422">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="168e8-423">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168e8-423">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168e8-424">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-424">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="168e8-425">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="168e8-425">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-426">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-426">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="168e8-427">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="168e8-427">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="168e8-428">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="168e8-428">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="168e8-429">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="168e8-429">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="168e8-430">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="168e8-430">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="168e8-431">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="168e8-431">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="168e8-432">**Не** выбирайте **Включение поддержки Docker**.</span><span class="sxs-lookup"><span data-stu-id="168e8-432">**Don't** select **Enable Docker Support**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="168e8-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168e8-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="168e8-435">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="168e8-435">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="168e8-436">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="168e8-436">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="168e8-437">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="168e8-437">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="168e8-438">С помощью этих команд создается новый проект веб-API и открывается новый экземпляр Visual Studio Code в новой папке проекта.</span><span class="sxs-lookup"><span data-stu-id="168e8-438">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="168e8-439">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="168e8-439">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168e8-440">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="168e8-441">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="168e8-441">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="168e8-443">Щелкните **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="168e8-443">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Диалоговое окно "Новый проект" в macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="168e8-445">В диалоговом окне **Настройка нового веб-API ASP.NET Core** оставьте установленное по умолчанию значение для параметра **Целевая платформа**, то есть \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="168e8-445">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="168e8-446">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="168e8-446">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="168e8-448">Тестирование API</span><span class="sxs-lookup"><span data-stu-id="168e8-448">Test the API</span></span>

<span data-ttu-id="168e8-449">Шаблон проекта создает API `values`.</span><span class="sxs-lookup"><span data-stu-id="168e8-449">The project template creates a `values` API.</span></span> <span data-ttu-id="168e8-450">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="168e8-450">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-451">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-451">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="168e8-452">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="168e8-452">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="168e8-453">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/api/values`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="168e8-453">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="168e8-454">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="168e8-454">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="168e8-455">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="168e8-455">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="168e8-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168e8-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="168e8-457">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="168e8-457">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="168e8-458">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="168e8-458">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168e8-459">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="168e8-460">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="168e8-460">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="168e8-461">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="168e8-461">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="168e8-462">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="168e8-462">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="168e8-463">Добавьте `/api/values` к URL-адресу (измените URL-адрес на `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="168e8-463">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="168e8-464">Возвращается следующий файл JSON:</span><span class="sxs-lookup"><span data-stu-id="168e8-464">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="168e8-465">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="168e8-465">Add a model class</span></span>

<span data-ttu-id="168e8-466">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="168e8-466">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="168e8-467">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="168e8-467">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="168e8-469">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="168e8-469">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="168e8-470">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="168e8-470">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="168e8-471">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="168e8-471">Name the folder *Models*.</span></span>

* <span data-ttu-id="168e8-472">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="168e8-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="168e8-473">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-473">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="168e8-474">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="168e8-474">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="168e8-475">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168e8-475">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="168e8-476">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="168e8-476">Add a folder named *Models*.</span></span>

* <span data-ttu-id="168e8-477">Добавьте класс `TodoItem` в папку *Models*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="168e8-477">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168e8-478">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-478">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="168e8-479">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="168e8-479">Right-click the project.</span></span> <span data-ttu-id="168e8-480">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="168e8-480">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="168e8-481">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="168e8-481">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="168e8-483">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="168e8-483">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="168e8-484">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="168e8-484">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="168e8-485">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="168e8-485">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="168e8-486">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="168e8-486">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="168e8-487">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models*.</span><span class="sxs-lookup"><span data-stu-id="168e8-487">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="168e8-488">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="168e8-488">Add a database context</span></span>

<span data-ttu-id="168e8-489">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="168e8-489">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="168e8-490">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="168e8-490">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-491">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-491">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="168e8-492">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="168e8-492">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="168e8-493">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-493">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="168e8-494">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-494">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="168e8-495">Добавьте класс `TodoContext` в папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="168e8-495">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="168e8-496">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="168e8-496">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="168e8-497">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="168e8-497">Register the database context</span></span>

<span data-ttu-id="168e8-498">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="168e8-498">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="168e8-499">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="168e8-499">The container provides the service to controllers.</span></span>

<span data-ttu-id="168e8-500">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="168e8-500">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="168e8-501">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="168e8-501">The preceding code:</span></span>

* <span data-ttu-id="168e8-502">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="168e8-502">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="168e8-503">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="168e8-503">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="168e8-504">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="168e8-504">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="168e8-505">Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="168e8-505">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-506">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="168e8-507">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="168e8-507">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="168e8-508">Щелкните **Добавить** > **Создать элемент**.</span><span class="sxs-lookup"><span data-stu-id="168e8-508">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="168e8-509">В диалоговом окне **Добавить новый элемент** выберите шаблон **Класс контроллера API**.</span><span class="sxs-lookup"><span data-stu-id="168e8-509">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="168e8-510">Присвойте классу имя *TodoController* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-510">Name the class *TodoController*, and select **Add**.</span></span>

  ![Диалоговое окно добавления элемента с контроллером в поле поиска и выбранным контроллером веб-API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="168e8-512">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-512">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="168e8-513">В папке *Controllers* создайте класс с именем `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="168e8-513">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="168e8-514">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="168e8-514">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="168e8-515">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="168e8-515">The preceding code:</span></span>

* <span data-ttu-id="168e8-516">Определяет класс контроллера API без методов.</span><span class="sxs-lookup"><span data-stu-id="168e8-516">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="168e8-517">Пометьте этот класс атрибутом [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="168e8-517">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="168e8-518">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="168e8-518">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="168e8-519">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="168e8-519">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="168e8-520">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="168e8-520">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="168e8-521">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="168e8-521">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="168e8-522">Добавляет элемент `Item1` в базу данных, если она пуста.</span><span class="sxs-lookup"><span data-stu-id="168e8-522">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="168e8-523">Этот код находится в конструкторе и выполняется каждый раз при обнаружении нового HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="168e8-523">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="168e8-524">Если вы удалите все элементы, конструктор создаст `Item1` при следующем вызове метода API.</span><span class="sxs-lookup"><span data-stu-id="168e8-524">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="168e8-525">Поэтому может создаться впечатление, что удаление не было выполнено, хотя это не так.</span><span class="sxs-lookup"><span data-stu-id="168e8-525">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="168e8-526">Добавление методов Get</span><span class="sxs-lookup"><span data-stu-id="168e8-526">Add Get methods</span></span>

<span data-ttu-id="168e8-527">Чтобы получить API, который извлекает элементы списка дел, добавьте следующие методы в класс `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="168e8-527">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="168e8-528">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="168e8-528">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="168e8-529">Если приложение все еще выполняется, оно останавливается.</span><span class="sxs-lookup"><span data-stu-id="168e8-529">Stop the app if it's still running.</span></span> <span data-ttu-id="168e8-530">Затем оно запускается снова с последними изменениями.</span><span class="sxs-lookup"><span data-stu-id="168e8-530">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="168e8-531">Протестируйте приложение, вызвав эти две конечные точки в браузере.</span><span class="sxs-lookup"><span data-stu-id="168e8-531">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="168e8-532">Пример:</span><span class="sxs-lookup"><span data-stu-id="168e8-532">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="168e8-533">При вызове `GetTodoItems` возвращается следующий ответ HTTP:</span><span class="sxs-lookup"><span data-stu-id="168e8-533">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="168e8-534">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="168e8-534">Routing and URL paths</span></span>

<span data-ttu-id="168e8-535">Атрибут [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="168e8-535">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="168e8-536">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="168e8-536">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="168e8-537">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="168e8-537">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="168e8-538">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="168e8-538">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="168e8-539">В этом примере класс контроллера носит имя **Todo**, а сам контроллер, соответственно, — "todo".</span><span class="sxs-lookup"><span data-stu-id="168e8-539">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="168e8-540">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="168e8-540">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="168e8-541">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="168e8-541">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="168e8-542">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="168e8-542">This sample doesn't use a template.</span></span> <span data-ttu-id="168e8-543">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="168e8-543">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="168e8-544">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="168e8-544">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="168e8-545">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="168e8-545">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="168e8-546">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="168e8-546">Return values</span></span>

<span data-ttu-id="168e8-547">Возвращаемое значение имеет тип `GetTodoItems`, а метод `GetTodoItem` имеет тип [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="168e8-547">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="168e8-548">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="168e8-548">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="168e8-549">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="168e8-549">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="168e8-550">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="168e8-550">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="168e8-551">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="168e8-551">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="168e8-552">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="168e8-552">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="168e8-553">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 ([Не найдено](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)).</span><span class="sxs-lookup"><span data-stu-id="168e8-553">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="168e8-554">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="168e8-554">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="168e8-555">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="168e8-555">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="168e8-556">Тестирование метода GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="168e8-556">Test the GetTodoItems method</span></span>

<span data-ttu-id="168e8-557">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="168e8-557">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="168e8-558">Установите [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="168e8-558">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="168e8-559">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="168e8-559">Start the web app.</span></span>
* <span data-ttu-id="168e8-560">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="168e8-560">Start Postman.</span></span>
* <span data-ttu-id="168e8-561">Отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="168e8-561">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168e8-562">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168e8-562">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="168e8-563">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="168e8-563">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="168e8-564">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="168e8-564">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="168e8-565">В меню **Postman** > **Настройки** (вкладка **Общие**) отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="168e8-565">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="168e8-566">Кроме того, можно выбрать значок гаечного ключа и выбрать **Параметры**, а затем отключить проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="168e8-566">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="168e8-567">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="168e8-567">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="168e8-568">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="168e8-568">Create a new request.</span></span>
  * <span data-ttu-id="168e8-569">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="168e8-569">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="168e8-570">Укажите URL-адрес запроса `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="168e8-570">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="168e8-571">Например, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="168e8-571">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="168e8-572">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="168e8-572">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="168e8-573">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-573">Select **Send**.</span></span>

![Postman с запросом Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="168e8-575">Добавление метода Create</span><span class="sxs-lookup"><span data-stu-id="168e8-575">Add a Create method</span></span>

<span data-ttu-id="168e8-576">Добавьте следующий метод `PostTodoItem` в *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="168e8-576">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="168e8-577">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="168e8-577">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="168e8-578">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="168e8-578">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="168e8-579">Метод `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="168e8-579">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="168e8-580">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="168e8-580">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="168e8-581">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="168e8-581">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="168e8-582">Добавляет заголовок `Location` в ответ.</span><span class="sxs-lookup"><span data-stu-id="168e8-582">Adds a `Location` header to the response.</span></span> <span data-ttu-id="168e8-583">Заголовок `Location` расположения указывает URI вновь созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="168e8-583">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="168e8-584">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="168e8-584">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="168e8-585">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="168e8-585">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="168e8-586">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="168e8-586">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="168e8-587">Тестирование метода PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="168e8-587">Test the PostTodoItem method</span></span>

* <span data-ttu-id="168e8-588">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="168e8-588">Build the project.</span></span>
* <span data-ttu-id="168e8-589">В Postman укажите метод HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="168e8-589">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="168e8-590">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="168e8-590">Select the **Body** tab.</span></span>
* <span data-ttu-id="168e8-591">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="168e8-591">Select the **raw** radio button.</span></span>
* <span data-ttu-id="168e8-592">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="168e8-592">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="168e8-593">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="168e8-593">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="168e8-594">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-594">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/create.png)

  <span data-ttu-id="168e8-596">Если вы получаете ошибку 405 "Недопустимый метод", это может свидетельствовать о том, что после добавления метода `PostTodoItem` не была выполнена компиляция проекта.</span><span class="sxs-lookup"><span data-stu-id="168e8-596">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="168e8-597">Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="168e8-597">Test the location header URI</span></span>

* <span data-ttu-id="168e8-598">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="168e8-598">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="168e8-599">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="168e8-599">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="168e8-601">Укажите метод GET.</span><span class="sxs-lookup"><span data-stu-id="168e8-601">Set the method to GET.</span></span>
* <span data-ttu-id="168e8-602">Вставьте URI (например, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="168e8-602">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="168e8-603">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-603">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="168e8-604">Добавление метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="168e8-604">Add a PutTodoItem method</span></span>

<span data-ttu-id="168e8-605">Добавьте приведенный ниже метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="168e8-605">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="168e8-606">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="168e8-606">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="168e8-607">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="168e8-607">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="168e8-608">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="168e8-608">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="168e8-609">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="168e8-609">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="168e8-610">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="168e8-610">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="168e8-611">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="168e8-611">Test the PutTodoItem method</span></span>

<span data-ttu-id="168e8-612">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="168e8-612">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="168e8-613">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="168e8-613">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="168e8-614">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="168e8-614">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="168e8-615">Обновите элемент списка дел с идентификатором = 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="168e8-615">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="168e8-616">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="168e8-616">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="168e8-618">Добавление метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="168e8-618">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="168e8-619">Добавьте приведенный ниже метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="168e8-619">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="168e8-620">`DeleteTodoItem`Ответ[ — 204 (нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="168e8-620">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="168e8-621">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="168e8-621">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="168e8-622">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="168e8-622">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="168e8-623">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="168e8-623">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="168e8-624">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="168e8-624">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="168e8-625">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="168e8-625">Select **Send**.</span></span>

<span data-ttu-id="168e8-626">В этом примере приложения вы можете удалить все элементы.</span><span class="sxs-lookup"><span data-stu-id="168e8-626">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="168e8-627">Однако в случае удаления последнего элемента в момент следующего вызова API конструктор класса модели создаст новый элемент.</span><span class="sxs-lookup"><span data-stu-id="168e8-627">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="168e8-628">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="168e8-628">Call the web API with JavaScript</span></span>

<span data-ttu-id="168e8-629">В этом разделе описано, как добавить HTML-страницу, которая использует JavaScript для вызова веб-API.</span><span class="sxs-lookup"><span data-stu-id="168e8-629">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="168e8-630">jQuery инициирует запрос.</span><span class="sxs-lookup"><span data-stu-id="168e8-630">jQuery initiates the request.</span></span> <span data-ttu-id="168e8-631">JavaScript изменяет страницу, используя сведения из ответа API.</span><span class="sxs-lookup"><span data-stu-id="168e8-631">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="168e8-632">Настройте приложение для [обслуживания статических файлов](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) и [включения сопоставления файлов по умолчанию](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), обновив *Startup.cs* следующим выделенным кодом:</span><span class="sxs-lookup"><span data-stu-id="168e8-632">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="168e8-633">Создайте папку *wwwroot* в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="168e8-633">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="168e8-634">Добавьте HTML-файл *index.html* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="168e8-634">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="168e8-635">Замените его содержимое следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="168e8-635">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="168e8-636">Добавьте файл JavaScript с именем *site.js* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="168e8-636">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="168e8-637">Замените его содержимое следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="168e8-637">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="168e8-638">Может потребоваться изменение параметров запуска проекта ASP.NET Core для локального тестирования HTML-страницы:</span><span class="sxs-lookup"><span data-stu-id="168e8-638">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="168e8-639">Откройте файл *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="168e8-639">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="168e8-640">Удалите свойство `launchUrl`, чтобы приложение открылось через *index.html* &mdash; файл проекта по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="168e8-640">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="168e8-641">В этом примере вызываются все методы CRUD в веб-API.</span><span class="sxs-lookup"><span data-stu-id="168e8-641">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="168e8-642">Ниже приводится пояснение вызовов API.</span><span class="sxs-lookup"><span data-stu-id="168e8-642">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="168e8-643">Получение списка элементов задач</span><span class="sxs-lookup"><span data-stu-id="168e8-643">Get a list of to-do items</span></span>

<span data-ttu-id="168e8-644">jQuery отправляет запрос HTTP GET к веб-API, который возвращает ответ JSON, представляющий массив элементов списка дел.</span><span class="sxs-lookup"><span data-stu-id="168e8-644">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="168e8-645">В случае успешного запроса используется функция обратного вызова `success`.</span><span class="sxs-lookup"><span data-stu-id="168e8-645">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="168e8-646">При обратном вызове в модель DOM вносятся данные о задачах.</span><span class="sxs-lookup"><span data-stu-id="168e8-646">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="168e8-647">Добавление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="168e8-647">Add a to-do item</span></span>

<span data-ttu-id="168e8-648">jQuery отправляет запрос HTTP POST с элементом списка дел в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="168e8-648">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="168e8-649">Для параметров `accepts` и `contentType` указывается `application/json`, чтобы указать тип носителя при получении и отправке.</span><span class="sxs-lookup"><span data-stu-id="168e8-649">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="168e8-650">Элемент списка дел преобразуется в JSON с помощью [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="168e8-650">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="168e8-651">Если интерфейс API возвращает код состояния успешного выполнения, вызывается функция `getData` для обновления HTML-таблицы.</span><span class="sxs-lookup"><span data-stu-id="168e8-651">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="168e8-652">Обновление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="168e8-652">Update a to-do item</span></span>

<span data-ttu-id="168e8-653">Обновление элемента списка дел выполняется аналогично его добавлению.</span><span class="sxs-lookup"><span data-stu-id="168e8-653">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="168e8-654">`url` изменяется, чтобы добавить уникальный идентификатор для элемента, а в качестве `type` устанавливается `PUT`.</span><span class="sxs-lookup"><span data-stu-id="168e8-654">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="168e8-655">Удаление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="168e8-655">Delete a to-do item</span></span>

<span data-ttu-id="168e8-656">Чтобы удалить элемент списка дел, установите для `type` в вызове AJAX значение `DELETE` и укажите уникальный идентификатор элемента в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="168e8-656">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="168e8-657">Добавление поддержки аутентификации в веб-API</span><span class="sxs-lookup"><span data-stu-id="168e8-657">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="168e8-658">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="168e8-658">Additional resources</span></span>

<span data-ttu-id="168e8-659">[Просмотреть или скачать пример кода для этого учебника](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="168e8-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="168e8-660">См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="168e8-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="168e8-661">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="168e8-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="168e8-662">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="168e8-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
