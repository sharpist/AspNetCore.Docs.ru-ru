---
title: Учебник. Создание веб-API с помощью ASP.NET Core
author: rick-anderson
description: Узнайте, как создать веб-API с помощью ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: 2fcfd46057935cadac76c558a78729a1c096ffc0
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451836"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="e9591-103">Учебник. Создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9591-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="e9591-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Майк Уоссон](https://github.com/mikewasson) (Mike Wasson)</span><span class="sxs-lookup"><span data-stu-id="e9591-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="e9591-105">В этом учебнике приводятся основные сведения о создании веб-API с помощью ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e9591-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e9591-106">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="e9591-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e9591-107">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="e9591-107">Create a web API project.</span></span>
> * <span data-ttu-id="e9591-108">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="e9591-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="e9591-109">Формирование шаблонов контроллера с использованием методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="e9591-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="e9591-110">Настройка маршрутизации, URL-пути и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="e9591-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="e9591-111">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="e9591-111">Call the web API with Postman.</span></span>

<span data-ttu-id="e9591-112">В итоге вы получите веб-API, позволяющий работать с элементами списка дел, хранимыми в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e9591-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="e9591-113">Обзор</span><span class="sxs-lookup"><span data-stu-id="e9591-113">Overview</span></span>

<span data-ttu-id="e9591-114">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="e9591-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="e9591-115">API</span><span class="sxs-lookup"><span data-stu-id="e9591-115">API</span></span> | <span data-ttu-id="e9591-116">Описание</span><span class="sxs-lookup"><span data-stu-id="e9591-116">Description</span></span> | <span data-ttu-id="e9591-117">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="e9591-117">Request body</span></span> | <span data-ttu-id="e9591-118">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="e9591-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="e9591-119">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="e9591-119">Get all to-do items</span></span> | <span data-ttu-id="e9591-120">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="e9591-120">None</span></span> | <span data-ttu-id="e9591-121">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="e9591-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="e9591-122">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="e9591-122">Get an item by ID</span></span> | <span data-ttu-id="e9591-123">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="e9591-123">None</span></span> | <span data-ttu-id="e9591-124">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="e9591-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="e9591-125">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="e9591-125">Add a new item</span></span> | <span data-ttu-id="e9591-126">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="e9591-126">To-do item</span></span> | <span data-ttu-id="e9591-127">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="e9591-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="e9591-128">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e9591-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="e9591-129">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="e9591-129">To-do item</span></span> | <span data-ttu-id="e9591-130">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="e9591-130">None</span></span> |
|<span data-ttu-id="e9591-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e9591-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="e9591-132">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e9591-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="e9591-133">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="e9591-133">None</span></span> | <span data-ttu-id="e9591-134">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="e9591-134">None</span></span>|

<span data-ttu-id="e9591-135">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="e9591-135">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="e9591-141">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="e9591-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9591-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9591-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e9591-144">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="e9591-145">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="e9591-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9591-147">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="e9591-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e9591-148">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="e9591-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="e9591-149">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e9591-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="e9591-150">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="e9591-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="e9591-151">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e9591-151">Select the **API** template and click **Create**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9591-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9591-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e9591-154">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e9591-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="e9591-155">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="e9591-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="e9591-156">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="e9591-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="e9591-157">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="e9591-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="e9591-158">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="e9591-158">The preceding commands:</span></span>

  * <span data-ttu-id="e9591-159">Создает новый проект веб-API и открывает его в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e9591-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="e9591-160">Добавляет пакеты NuGet, которые понадобятся в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="e9591-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e9591-161">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e9591-162">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="e9591-162">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="e9591-164">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="e9591-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="e9591-165">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="e9591-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![Выбор шаблона API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="e9591-167">Убедитесь, что для параметра **Требуемая версия .NET Framework** выбрано **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="e9591-167">Confirm the **Target Framework** is set to **.NET Core 3.1**.</span></span> <span data-ttu-id="e9591-168">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="e9591-168">Select **Next**.</span></span>

  ![Выбор .NET Core 3.1 для macOS](first-web-api-mac/_static/api_31_config.png)

* <span data-ttu-id="e9591-170">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e9591-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="e9591-172">Откройте командный терминал в папке проекта и выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="e9591-172">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="e9591-173">Тестирование API</span><span class="sxs-lookup"><span data-stu-id="e9591-173">Test the API</span></span>

<span data-ttu-id="e9591-174">Шаблон проекта создает API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="e9591-174">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="e9591-175">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="e9591-175">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e9591-177">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="e9591-177">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e9591-178">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/WeatherForecast`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="e9591-178">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="e9591-179">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="e9591-179">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="e9591-180">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="e9591-180">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9591-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9591-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e9591-182">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="e9591-182">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e9591-183">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="e9591-183">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e9591-184">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-184">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e9591-185">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="e9591-185">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="e9591-186">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="e9591-186">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="e9591-187">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="e9591-187">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="e9591-188">Добавьте `/WeatherForecast` к URL-адресу (измените URL-адрес на `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="e9591-188">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="e9591-189">Возвращаемые данные JSON будут выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="e9591-189">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="e9591-190">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="e9591-190">Add a model class</span></span>

<span data-ttu-id="e9591-191">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="e9591-191">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="e9591-192">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e9591-192">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9591-194">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="e9591-194">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="e9591-195">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="e9591-195">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e9591-196">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e9591-196">Name the folder *Models*.</span></span>

* <span data-ttu-id="e9591-197">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="e9591-197">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e9591-198">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-198">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="e9591-199">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="e9591-199">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9591-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9591-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e9591-201">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="e9591-201">Add a folder named *Models*.</span></span>

* <span data-ttu-id="e9591-202">Добавьте класс `TodoItem` в папку *Models*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="e9591-202">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e9591-203">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e9591-204">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="e9591-204">Right-click the project.</span></span> <span data-ttu-id="e9591-205">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="e9591-205">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e9591-206">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e9591-206">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="e9591-208">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="e9591-208">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="e9591-209">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e9591-209">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="e9591-210">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="e9591-210">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="e9591-211">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="e9591-211">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="e9591-212">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models*.</span><span class="sxs-lookup"><span data-stu-id="e9591-212">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="e9591-213">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="e9591-213">Add a database context</span></span>

<span data-ttu-id="e9591-214">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="e9591-214">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="e9591-215">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e9591-215">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-216">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="e9591-217">Добавление Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="e9591-217">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="e9591-218">В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.</span><span class="sxs-lookup"><span data-stu-id="e9591-218">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="e9591-219">Перейдите на вкладку **Обзор** и введите **Microsoft.EntityFrameworkCore.SqlServer** в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="e9591-219">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="e9591-220">На панели слева выберите **Microsoft.EntityFrameworkCore.SqlServer**.</span><span class="sxs-lookup"><span data-stu-id="e9591-220">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="e9591-221">Установите флажок **Проект** на правой панели и выберите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-221">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="e9591-222">Добавьте пакет NuGet `Microsoft.EntityFrameworkCore.InMemory` согласно инструкциям выше.</span><span class="sxs-lookup"><span data-stu-id="e9591-222">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Диспетчер пакетов NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="e9591-224">Добавление контекста базы данных TodoContext</span><span class="sxs-lookup"><span data-stu-id="e9591-224">Add the TodoContext database context</span></span>

* <span data-ttu-id="e9591-225">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="e9591-225">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e9591-226">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-226">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e9591-227">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-227">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e9591-228">Добавьте класс `TodoContext` в папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="e9591-228">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="e9591-229">Введите следующий код:</span><span class="sxs-lookup"><span data-stu-id="e9591-229">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="e9591-230">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="e9591-230">Register the database context</span></span>

<span data-ttu-id="e9591-231">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e9591-231">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e9591-232">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="e9591-232">The container provides the service to controllers.</span></span>

<span data-ttu-id="e9591-233">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="e9591-233">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="e9591-234">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="e9591-234">The preceding code:</span></span>

* <span data-ttu-id="e9591-235">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="e9591-235">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="e9591-236">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e9591-236">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="e9591-237">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="e9591-237">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="e9591-238">Формирование шаблонов контроллера</span><span class="sxs-lookup"><span data-stu-id="e9591-238">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-239">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9591-240">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="e9591-240">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="e9591-241">Щелкните **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="e9591-241">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e9591-242">Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-242">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="e9591-243">В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="e9591-243">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="e9591-244">Выберите **TodoItem (TodoApi.Models)** в поле **Класс модели**.</span><span class="sxs-lookup"><span data-stu-id="e9591-244">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="e9591-245">Выберите **TodoContext (TodoApi.Models)** в поле **Класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="e9591-245">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="e9591-246">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-246">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e9591-247">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e9591-248">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="e9591-248">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="e9591-249">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="e9591-249">The preceding commands:</span></span>

* <span data-ttu-id="e9591-250">добавляют пакеты NuGet, необходимые для формирования шаблонов;</span><span class="sxs-lookup"><span data-stu-id="e9591-250">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="e9591-251">устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);</span><span class="sxs-lookup"><span data-stu-id="e9591-251">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="e9591-252">формируют шаблоны для `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="e9591-252">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="e9591-253">Сформированный код:</span><span class="sxs-lookup"><span data-stu-id="e9591-253">The generated code:</span></span>

* <span data-ttu-id="e9591-254">Пометьте этот класс атрибутом [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="e9591-254">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="e9591-255">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="e9591-255">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="e9591-256">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="e9591-256">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="e9591-257">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="e9591-257">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="e9591-258">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="e9591-258">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="e9591-259">Шаблоны ASP.NET Core для:</span><span class="sxs-lookup"><span data-stu-id="e9591-259">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="e9591-260">Контроллеры с представлениями включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="e9591-260">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="e9591-261">Контроллеры API не включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="e9591-261">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="e9591-262">Если токен `[action]` не находится в шаблоне маршрута, имя [действия](xref:mvc/controllers/routing#action) исключается из маршрута.</span><span class="sxs-lookup"><span data-stu-id="e9591-262">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="e9591-263">То есть имя связанного метода действия не используется в соответствующем маршруте.</span><span class="sxs-lookup"><span data-stu-id="e9591-263">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="e9591-264">Знакомство с методом создания PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="e9591-264">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="e9591-265">Измените инструкцию возврата в `PostTodoItem` и используйте оператор [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="e9591-265">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="e9591-266">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="e9591-266">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="e9591-267">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="e9591-267">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="e9591-268">Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="e9591-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="e9591-269">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="e9591-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="e9591-270">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="e9591-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="e9591-271">Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location).</span><span class="sxs-lookup"><span data-stu-id="e9591-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="e9591-272">Заголовок `Location` указывает [URI](https://developer.mozilla.org/docs/Glossary/URI) новой созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="e9591-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="e9591-273">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e9591-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="e9591-274">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="e9591-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="e9591-275">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="e9591-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="e9591-276">Установка Postman</span><span class="sxs-lookup"><span data-stu-id="e9591-276">Install Postman</span></span>

<span data-ttu-id="e9591-277">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="e9591-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="e9591-278">Установка [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="e9591-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="e9591-279">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="e9591-279">Start the web app.</span></span>
* <span data-ttu-id="e9591-280">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="e9591-280">Start Postman.</span></span>
* <span data-ttu-id="e9591-281">Отключение параметра **Проверка SSL-сертификата**</span><span class="sxs-lookup"><span data-stu-id="e9591-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="e9591-282">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="e9591-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="e9591-283">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="e9591-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="e9591-284">Тестирование PostTodoItem с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="e9591-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="e9591-285">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="e9591-285">Create a new request.</span></span>
* <span data-ttu-id="e9591-286">Установите HTTP-метод `POST`.</span><span class="sxs-lookup"><span data-stu-id="e9591-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="e9591-287">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="e9591-287">Select the **Body** tab.</span></span>
* <span data-ttu-id="e9591-288">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="e9591-288">Select the **raw** radio button.</span></span>
* <span data-ttu-id="e9591-289">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="e9591-289">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="e9591-290">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="e9591-290">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="e9591-291">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-291">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="e9591-293">Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="e9591-293">Test the location header URI</span></span>

* <span data-ttu-id="e9591-294">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="e9591-294">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="e9591-295">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="e9591-295">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="e9591-297">Укажите метод GET.</span><span class="sxs-lookup"><span data-stu-id="e9591-297">Set the method to GET.</span></span>
* <span data-ttu-id="e9591-298">Вставьте URI (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="e9591-298">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="e9591-299">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-299">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="e9591-300">Знакомство с методами GET</span><span class="sxs-lookup"><span data-stu-id="e9591-300">Examine the GET methods</span></span>

<span data-ttu-id="e9591-301">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="e9591-301">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="e9591-302">Протестируйте приложение, вызвав эти две конечные точки в браузере или в Postman.</span><span class="sxs-lookup"><span data-stu-id="e9591-302">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="e9591-303">Пример:</span><span class="sxs-lookup"><span data-stu-id="e9591-303">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="e9591-304">При вызове `GetTodoItems` возвращается примерно такой ответ:</span><span class="sxs-lookup"><span data-stu-id="e9591-304">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="e9591-305">Тестирование Get с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="e9591-305">Test Get with Postman</span></span>

* <span data-ttu-id="e9591-306">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="e9591-306">Create a new request.</span></span>
* <span data-ttu-id="e9591-307">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="e9591-307">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="e9591-308">Укажите URL-адрес запроса `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="e9591-308">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="e9591-309">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="e9591-309">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="e9591-310">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="e9591-310">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="e9591-311">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-311">Select **Send**.</span></span>

<span data-ttu-id="e9591-312">Это приложение использует выполняющуюся в памяти базу данных.</span><span class="sxs-lookup"><span data-stu-id="e9591-312">This app uses an in-memory database.</span></span> <span data-ttu-id="e9591-313">Если остановить и вновь запустить его, предшествующий запрос GET не возвратит никаких данных.</span><span class="sxs-lookup"><span data-stu-id="e9591-313">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="e9591-314">Если данные не возвращаются, данные для приложения получаются методом [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="e9591-314">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="e9591-315">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="e9591-315">Routing and URL paths</span></span>

<span data-ttu-id="e9591-316">Атрибут [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="e9591-316">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="e9591-317">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="e9591-317">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="e9591-318">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="e9591-318">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="e9591-319">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="e9591-319">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="e9591-320">В этом примере класс контроллера имеет имя **TodoItems**, а сам контроллер, соответственно, — "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="e9591-320">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="e9591-321">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="e9591-321">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="e9591-322">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="e9591-322">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="e9591-323">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="e9591-323">This sample doesn't use a template.</span></span> <span data-ttu-id="e9591-324">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e9591-324">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e9591-325">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="e9591-325">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="e9591-326">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="e9591-326">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="e9591-327">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="e9591-327">Return values</span></span>

<span data-ttu-id="e9591-328">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="e9591-328">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="e9591-329">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="e9591-329">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="e9591-330">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="e9591-330">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="e9591-331">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="e9591-331">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="e9591-332">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="e9591-332">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="e9591-333">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="e9591-333">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="e9591-334">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 ([Не найдено](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)).</span><span class="sxs-lookup"><span data-stu-id="e9591-334">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="e9591-335">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="e9591-335">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="e9591-336">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="e9591-336">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="e9591-337">Метод PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e9591-337">The PutTodoItem method</span></span>

<span data-ttu-id="e9591-338">Проверьте метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e9591-338">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="e9591-339">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="e9591-339">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="e9591-340">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e9591-340">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="e9591-341">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="e9591-341">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="e9591-342">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="e9591-342">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="e9591-343">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="e9591-343">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="e9591-344">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e9591-344">Test the PutTodoItem method</span></span>

<span data-ttu-id="e9591-345">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="e9591-345">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="e9591-346">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="e9591-346">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="e9591-347">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e9591-347">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="e9591-348">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="e9591-348">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="e9591-349">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="e9591-349">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="e9591-351">Метод DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e9591-351">The DeleteTodoItem method</span></span>

<span data-ttu-id="e9591-352">Проверьте метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e9591-352">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="e9591-353">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e9591-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="e9591-354">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="e9591-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="e9591-355">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="e9591-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="e9591-356">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="e9591-356">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="e9591-357">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-357">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="e9591-358">Предотвращение избыточной публикации</span><span class="sxs-lookup"><span data-stu-id="e9591-358">Prevent over-posting</span></span>

<span data-ttu-id="e9591-359">В настоящее время пример приложения предоставляет весь объект `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e9591-359">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="e9591-360">Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели.</span><span class="sxs-lookup"><span data-stu-id="e9591-360">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="e9591-361">Это связано с несколькими причинами, и безопасность является основной.</span><span class="sxs-lookup"><span data-stu-id="e9591-361">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="e9591-362">Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления.</span><span class="sxs-lookup"><span data-stu-id="e9591-362">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="e9591-363">В этой статье используется **DTO**.</span><span class="sxs-lookup"><span data-stu-id="e9591-363">**DTO** is used in this article.</span></span>

<span data-ttu-id="e9591-364">DTO можно использовать для следующего:</span><span class="sxs-lookup"><span data-stu-id="e9591-364">A DTO may be used to:</span></span>

* <span data-ttu-id="e9591-365">Предотвращение избыточной публикации.</span><span class="sxs-lookup"><span data-stu-id="e9591-365">Prevent over-posting.</span></span>
* <span data-ttu-id="e9591-366">Скрытие свойств, которые не предназначены для просмотра клиентами.</span><span class="sxs-lookup"><span data-stu-id="e9591-366">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="e9591-367">Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="e9591-367">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="e9591-368">Сведение графов объектов, содержащих вложенные объекты.</span><span class="sxs-lookup"><span data-stu-id="e9591-368">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="e9591-369">Сведенные графы объектов могут быть удобнее для клиентов.</span><span class="sxs-lookup"><span data-stu-id="e9591-369">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="e9591-370">Чтобы продемонстрировать подход с применением DTO, обновите класс `TodoItem`, включив в него поле секрета:</span><span class="sxs-lookup"><span data-stu-id="e9591-370">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="e9591-371">Поле секрета должно быть скрыто в этом приложении, однако административное приложение может отобразить его.</span><span class="sxs-lookup"><span data-stu-id="e9591-371">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="e9591-372">Убедитесь, что вы можете отправить и получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="e9591-372">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="e9591-373">Создайте модель DTO:</span><span class="sxs-lookup"><span data-stu-id="e9591-373">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="e9591-374">Обновите `TodoItemsController` для использования `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="e9591-374">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="e9591-375">Убедитесь, что вы можете отправить или получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="e9591-375">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="e9591-376">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="e9591-376">Call the web API with JavaScript</span></span>

<span data-ttu-id="e9591-377">См. руководство по [: Вызовите веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="e9591-377">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e9591-378">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="e9591-378">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e9591-379">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="e9591-379">Create a web API project.</span></span>
> * <span data-ttu-id="e9591-380">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="e9591-380">Add a model class and a database context.</span></span>
> * <span data-ttu-id="e9591-381">Добавление контроллера.</span><span class="sxs-lookup"><span data-stu-id="e9591-381">Add a controller.</span></span>
> * <span data-ttu-id="e9591-382">Добавление методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="e9591-382">Add CRUD methods.</span></span>
> * <span data-ttu-id="e9591-383">Настройка маршрутизации и путей URL.</span><span class="sxs-lookup"><span data-stu-id="e9591-383">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="e9591-384">Указание возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="e9591-384">Specify return values.</span></span>
> * <span data-ttu-id="e9591-385">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="e9591-385">Call the web API with Postman.</span></span>
> * <span data-ttu-id="e9591-386">Вызовите веб-API с помощью JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e9591-386">Call the web API with JavaScript.</span></span>

<span data-ttu-id="e9591-387">В конечном итоге вы получите веб-API, обеспечивающий управление элементами списка дел, хранящимися в реляционной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e9591-387">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="e9591-388">Обзор</span><span class="sxs-lookup"><span data-stu-id="e9591-388">Overview</span></span>

<span data-ttu-id="e9591-389">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="e9591-389">This tutorial creates the following API:</span></span>

|<span data-ttu-id="e9591-390">API</span><span class="sxs-lookup"><span data-stu-id="e9591-390">API</span></span> | <span data-ttu-id="e9591-391">Описание</span><span class="sxs-lookup"><span data-stu-id="e9591-391">Description</span></span> | <span data-ttu-id="e9591-392">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="e9591-392">Request body</span></span> | <span data-ttu-id="e9591-393">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="e9591-393">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="e9591-394">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e9591-394">GET /api/TodoItems</span></span> | <span data-ttu-id="e9591-395">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="e9591-395">Get all to-do items</span></span> | <span data-ttu-id="e9591-396">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="e9591-396">None</span></span> | <span data-ttu-id="e9591-397">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="e9591-397">Array of to-do items</span></span>|
|<span data-ttu-id="e9591-398">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e9591-398">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="e9591-399">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="e9591-399">Get an item by ID</span></span> | <span data-ttu-id="e9591-400">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="e9591-400">None</span></span> | <span data-ttu-id="e9591-401">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="e9591-401">To-do item</span></span>|
|<span data-ttu-id="e9591-402">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e9591-402">POST /api/TodoItems</span></span> | <span data-ttu-id="e9591-403">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="e9591-403">Add a new item</span></span> | <span data-ttu-id="e9591-404">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="e9591-404">To-do item</span></span> | <span data-ttu-id="e9591-405">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="e9591-405">To-do item</span></span> |
|<span data-ttu-id="e9591-406">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e9591-406">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="e9591-407">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e9591-407">Update an existing item &nbsp;</span></span> | <span data-ttu-id="e9591-408">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="e9591-408">To-do item</span></span> | <span data-ttu-id="e9591-409">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="e9591-409">None</span></span> |
|<span data-ttu-id="e9591-410">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e9591-410">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="e9591-411">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e9591-411">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="e9591-412">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="e9591-412">None</span></span> | <span data-ttu-id="e9591-413">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="e9591-413">None</span></span>|

<span data-ttu-id="e9591-414">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="e9591-414">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="e9591-420">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="e9591-420">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-421">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9591-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9591-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e9591-423">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-423">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="e9591-424">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="e9591-424">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-425">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-425">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9591-426">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="e9591-426">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e9591-427">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="e9591-427">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="e9591-428">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e9591-428">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="e9591-429">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="e9591-429">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="e9591-430">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e9591-430">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="e9591-431">**Не** выбирайте **Включение поддержки Docker**.</span><span class="sxs-lookup"><span data-stu-id="e9591-431">**Don't** select **Enable Docker Support**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9591-433">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9591-433">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e9591-434">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e9591-434">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="e9591-435">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="e9591-435">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="e9591-436">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="e9591-436">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="e9591-437">С помощью этих команд создается новый проект веб-API и открывается новый экземпляр Visual Studio Code в новой папке проекта.</span><span class="sxs-lookup"><span data-stu-id="e9591-437">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="e9591-438">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="e9591-438">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e9591-439">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e9591-440">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="e9591-440">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="e9591-442">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="e9591-442">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="e9591-443">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="e9591-443">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="e9591-444">В диалоговом окне **Настройка нового веб-API ASP.NET Core** оставьте установленное по умолчанию значение для параметра **Целевая платформа**, то есть \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="e9591-444">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="e9591-445">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e9591-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="e9591-447">Тестирование API</span><span class="sxs-lookup"><span data-stu-id="e9591-447">Test the API</span></span>

<span data-ttu-id="e9591-448">Шаблон проекта создает API `values`.</span><span class="sxs-lookup"><span data-stu-id="e9591-448">The project template creates a `values` API.</span></span> <span data-ttu-id="e9591-449">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="e9591-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e9591-451">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="e9591-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e9591-452">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/api/values`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="e9591-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="e9591-453">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="e9591-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="e9591-454">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="e9591-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9591-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9591-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e9591-456">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="e9591-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e9591-457">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="e9591-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e9591-458">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e9591-459">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="e9591-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="e9591-460">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="e9591-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="e9591-461">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="e9591-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="e9591-462">Добавьте `/api/values` к URL-адресу (измените URL-адрес на `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="e9591-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="e9591-463">Возвращается следующий файл JSON:</span><span class="sxs-lookup"><span data-stu-id="e9591-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="e9591-464">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="e9591-464">Add a model class</span></span>

<span data-ttu-id="e9591-465">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="e9591-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="e9591-466">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e9591-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9591-468">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="e9591-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="e9591-469">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="e9591-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e9591-470">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e9591-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="e9591-471">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="e9591-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e9591-472">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="e9591-473">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="e9591-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9591-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9591-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e9591-475">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="e9591-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="e9591-476">Добавьте класс `TodoItem` в папку *Models*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="e9591-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e9591-477">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e9591-478">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="e9591-478">Right-click the project.</span></span> <span data-ttu-id="e9591-479">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="e9591-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e9591-480">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e9591-480">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="e9591-482">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="e9591-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="e9591-483">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e9591-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="e9591-484">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="e9591-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="e9591-485">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="e9591-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="e9591-486">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models*.</span><span class="sxs-lookup"><span data-stu-id="e9591-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="e9591-487">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="e9591-487">Add a database context</span></span>

<span data-ttu-id="e9591-488">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="e9591-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="e9591-489">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e9591-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9591-491">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="e9591-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e9591-492">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e9591-493">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e9591-494">Добавьте класс `TodoContext` в папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="e9591-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="e9591-495">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="e9591-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="e9591-496">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="e9591-496">Register the database context</span></span>

<span data-ttu-id="e9591-497">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e9591-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e9591-498">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="e9591-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="e9591-499">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="e9591-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="e9591-500">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="e9591-500">The preceding code:</span></span>

* <span data-ttu-id="e9591-501">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="e9591-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="e9591-502">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e9591-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="e9591-503">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="e9591-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="e9591-504">Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="e9591-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9591-506">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="e9591-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="e9591-507">Щелкните **Добавить** > **Создать элемент**.</span><span class="sxs-lookup"><span data-stu-id="e9591-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="e9591-508">В диалоговом окне **Добавить новый элемент** выберите шаблон **Класс контроллера API**.</span><span class="sxs-lookup"><span data-stu-id="e9591-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="e9591-509">Присвойте классу имя *TodoController* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Диалоговое окно добавления элемента с контроллером в поле поиска и выбранным контроллером веб-API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e9591-511">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e9591-512">В папке *Controllers* создайте класс с именем `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="e9591-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="e9591-513">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="e9591-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="e9591-514">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="e9591-514">The preceding code:</span></span>

* <span data-ttu-id="e9591-515">Определяет класс контроллера API без методов.</span><span class="sxs-lookup"><span data-stu-id="e9591-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="e9591-516">Пометьте этот класс атрибутом [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="e9591-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="e9591-517">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="e9591-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="e9591-518">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="e9591-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="e9591-519">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="e9591-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="e9591-520">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="e9591-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="e9591-521">Добавляет элемент `Item1` в базу данных, если она пуста.</span><span class="sxs-lookup"><span data-stu-id="e9591-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="e9591-522">Этот код находится в конструкторе и выполняется каждый раз при обнаружении нового HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="e9591-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="e9591-523">Если вы удалите все элементы, конструктор создаст `Item1` при следующем вызове метода API.</span><span class="sxs-lookup"><span data-stu-id="e9591-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="e9591-524">Поэтому может создаться впечатление, что удаление не было выполнено, хотя это не так.</span><span class="sxs-lookup"><span data-stu-id="e9591-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="e9591-525">Добавление методов Get</span><span class="sxs-lookup"><span data-stu-id="e9591-525">Add Get methods</span></span>

<span data-ttu-id="e9591-526">Чтобы получить API, который извлекает элементы списка дел, добавьте следующие методы в класс `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="e9591-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="e9591-527">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="e9591-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="e9591-528">Если приложение все еще выполняется, оно останавливается.</span><span class="sxs-lookup"><span data-stu-id="e9591-528">Stop the app if it's still running.</span></span> <span data-ttu-id="e9591-529">Затем оно запускается снова с последними изменениями.</span><span class="sxs-lookup"><span data-stu-id="e9591-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="e9591-530">Протестируйте приложение, вызвав эти две конечные точки в браузере.</span><span class="sxs-lookup"><span data-stu-id="e9591-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="e9591-531">Пример:</span><span class="sxs-lookup"><span data-stu-id="e9591-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="e9591-532">При вызове `GetTodoItems` возвращается следующий ответ HTTP:</span><span class="sxs-lookup"><span data-stu-id="e9591-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="e9591-533">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="e9591-533">Routing and URL paths</span></span>

<span data-ttu-id="e9591-534">Атрибут [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="e9591-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="e9591-535">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="e9591-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="e9591-536">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="e9591-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="e9591-537">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="e9591-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="e9591-538">В этом примере класс контроллера носит имя **Todo**, а сам контроллер, соответственно, — "todo".</span><span class="sxs-lookup"><span data-stu-id="e9591-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="e9591-539">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="e9591-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="e9591-540">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="e9591-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="e9591-541">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="e9591-541">This sample doesn't use a template.</span></span> <span data-ttu-id="e9591-542">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e9591-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e9591-543">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="e9591-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="e9591-544">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="e9591-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="e9591-545">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="e9591-545">Return values</span></span>

<span data-ttu-id="e9591-546">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="e9591-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="e9591-547">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="e9591-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="e9591-548">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="e9591-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="e9591-549">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="e9591-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="e9591-550">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="e9591-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="e9591-551">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="e9591-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="e9591-552">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 ([Не найдено](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)).</span><span class="sxs-lookup"><span data-stu-id="e9591-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="e9591-553">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="e9591-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="e9591-554">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="e9591-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="e9591-555">Тестирование метода GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="e9591-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="e9591-556">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="e9591-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="e9591-557">Установите [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="e9591-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="e9591-558">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="e9591-558">Start the web app.</span></span>
* <span data-ttu-id="e9591-559">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="e9591-559">Start Postman.</span></span>
* <span data-ttu-id="e9591-560">Отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="e9591-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9591-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9591-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9591-562">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="e9591-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e9591-563">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e9591-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e9591-564">В меню **Postman** > **Настройки** (вкладка **Общие**) отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="e9591-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="e9591-565">Кроме того, можно выбрать значок гаечного ключа и выбрать **Параметры**, а затем отключить проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="e9591-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="e9591-566">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="e9591-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="e9591-567">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="e9591-567">Create a new request.</span></span>
  * <span data-ttu-id="e9591-568">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="e9591-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="e9591-569">Укажите URL-адрес запроса `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="e9591-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="e9591-570">Например, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="e9591-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="e9591-571">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="e9591-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="e9591-572">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-572">Select **Send**.</span></span>

![Postman с запросом Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="e9591-574">Добавление метода Create</span><span class="sxs-lookup"><span data-stu-id="e9591-574">Add a Create method</span></span>

<span data-ttu-id="e9591-575">Добавьте следующий метод `PostTodoItem` в *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="e9591-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="e9591-576">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="e9591-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="e9591-577">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="e9591-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="e9591-578">Метод `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="e9591-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="e9591-579">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="e9591-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="e9591-580">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="e9591-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="e9591-581">Добавляет заголовок `Location` в ответ.</span><span class="sxs-lookup"><span data-stu-id="e9591-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="e9591-582">Заголовок `Location` расположения указывает URI вновь созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="e9591-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="e9591-583">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e9591-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="e9591-584">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="e9591-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="e9591-585">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="e9591-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="e9591-586">Тестирование метода PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="e9591-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="e9591-587">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="e9591-587">Build the project.</span></span>
* <span data-ttu-id="e9591-588">В Postman укажите метод HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="e9591-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="e9591-589">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="e9591-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="e9591-590">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="e9591-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="e9591-591">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="e9591-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="e9591-592">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="e9591-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="e9591-593">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-593">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/create.png)

  <span data-ttu-id="e9591-595">Если вы получаете ошибку 405 "Недопустимый метод", это может свидетельствовать о том, что после добавления метода `PostTodoItem` не была выполнена компиляция проекта.</span><span class="sxs-lookup"><span data-stu-id="e9591-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="e9591-596">Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="e9591-596">Test the location header URI</span></span>

* <span data-ttu-id="e9591-597">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="e9591-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="e9591-598">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="e9591-598">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="e9591-600">Укажите метод GET.</span><span class="sxs-lookup"><span data-stu-id="e9591-600">Set the method to GET.</span></span>
* <span data-ttu-id="e9591-601">Вставьте URI (например, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="e9591-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="e9591-602">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="e9591-603">Добавление метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e9591-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="e9591-604">Добавьте приведенный ниже метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e9591-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="e9591-605">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="e9591-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="e9591-606">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e9591-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="e9591-607">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="e9591-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="e9591-608">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="e9591-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="e9591-609">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="e9591-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="e9591-610">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e9591-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="e9591-611">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="e9591-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="e9591-612">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="e9591-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="e9591-613">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e9591-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="e9591-614">Обновите элемент списка дел с идентификатором = 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="e9591-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="e9591-615">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="e9591-615">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="e9591-617">Добавление метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e9591-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="e9591-618">Добавьте приведенный ниже метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e9591-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="e9591-619">`DeleteTodoItem`Ответ[ — 204 (нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e9591-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="e9591-620">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e9591-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="e9591-621">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="e9591-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="e9591-622">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="e9591-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="e9591-623">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="e9591-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="e9591-624">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="e9591-624">Select **Send**.</span></span>

<span data-ttu-id="e9591-625">В этом примере приложения вы можете удалить все элементы.</span><span class="sxs-lookup"><span data-stu-id="e9591-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="e9591-626">Однако в случае удаления последнего элемента в момент следующего вызова API конструктор класса модели создаст новый элемент.</span><span class="sxs-lookup"><span data-stu-id="e9591-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="e9591-627">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="e9591-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="e9591-628">В этом разделе описано, как добавить HTML-страницу, которая использует JavaScript для вызова веб-API.</span><span class="sxs-lookup"><span data-stu-id="e9591-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="e9591-629">jQuery инициирует запрос.</span><span class="sxs-lookup"><span data-stu-id="e9591-629">jQuery initiates the request.</span></span> <span data-ttu-id="e9591-630">JavaScript изменяет страницу, используя сведения из ответа API.</span><span class="sxs-lookup"><span data-stu-id="e9591-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="e9591-631">Настройте приложение для [обслуживания статических файлов](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) и [включения сопоставления файлов по умолчанию](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), обновив *Startup.cs* следующим выделенным кодом:</span><span class="sxs-lookup"><span data-stu-id="e9591-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="e9591-632">Создайте папку *wwwroot* в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="e9591-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="e9591-633">Добавьте HTML-файл *index.html* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e9591-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="e9591-634">Замените его содержимое следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="e9591-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="e9591-635">Добавьте файл JavaScript с именем *site.js* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e9591-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="e9591-636">Замените его содержимое следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="e9591-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="e9591-637">Может потребоваться изменение параметров запуска проекта ASP.NET Core для локального тестирования HTML-страницы:</span><span class="sxs-lookup"><span data-stu-id="e9591-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="e9591-638">Откройте файл *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e9591-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="e9591-639">Удалите свойство `launchUrl`, чтобы приложение открылось через *index.html* &mdash; файл проекта по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e9591-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="e9591-640">В этом примере вызываются все методы CRUD в веб-API.</span><span class="sxs-lookup"><span data-stu-id="e9591-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="e9591-641">Ниже приводится пояснение вызовов API.</span><span class="sxs-lookup"><span data-stu-id="e9591-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="e9591-642">Получение списка элементов задач</span><span class="sxs-lookup"><span data-stu-id="e9591-642">Get a list of to-do items</span></span>

<span data-ttu-id="e9591-643">jQuery отправляет запрос HTTP GET к веб-API, который возвращает ответ JSON, представляющий массив элементов списка дел.</span><span class="sxs-lookup"><span data-stu-id="e9591-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="e9591-644">В случае успешного запроса используется функция обратного вызова `success`.</span><span class="sxs-lookup"><span data-stu-id="e9591-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="e9591-645">При обратном вызове в модель DOM вносятся данные о задачах.</span><span class="sxs-lookup"><span data-stu-id="e9591-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="e9591-646">Добавление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="e9591-646">Add a to-do item</span></span>

<span data-ttu-id="e9591-647">jQuery отправляет запрос HTTP POST с элементом списка дел в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="e9591-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="e9591-648">Для параметров `accepts` и `contentType` указывается `application/json`, чтобы указать тип носителя при получении и отправке.</span><span class="sxs-lookup"><span data-stu-id="e9591-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="e9591-649">Элемент списка дел преобразуется в JSON с помощью [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="e9591-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="e9591-650">Если интерфейс API возвращает код состояния успешного выполнения, вызывается функция `getData` для обновления HTML-таблицы.</span><span class="sxs-lookup"><span data-stu-id="e9591-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="e9591-651">Обновление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="e9591-651">Update a to-do item</span></span>

<span data-ttu-id="e9591-652">Обновление элемента списка дел выполняется аналогично его добавлению.</span><span class="sxs-lookup"><span data-stu-id="e9591-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="e9591-653">`url` изменяется, чтобы добавить уникальный идентификатор для элемента, а в качестве `type` устанавливается `PUT`.</span><span class="sxs-lookup"><span data-stu-id="e9591-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="e9591-654">Удаление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="e9591-654">Delete a to-do item</span></span>

<span data-ttu-id="e9591-655">Чтобы удалить элемент списка дел, установите для `type` в вызове AJAX значение `DELETE` и укажите уникальный идентификатор элемента в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="e9591-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="e9591-656">Добавление поддержки аутентификации в веб-API</span><span class="sxs-lookup"><span data-stu-id="e9591-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="e9591-657">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e9591-657">Additional resources</span></span>

<span data-ttu-id="e9591-658">[Просмотреть или скачать пример кода для этого учебника](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="e9591-658">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="e9591-659">См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="e9591-659">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="e9591-660">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="e9591-660">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="e9591-661">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="e9591-661">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
