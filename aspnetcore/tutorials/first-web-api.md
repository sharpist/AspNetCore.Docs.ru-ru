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
ms.openlocfilehash: ddc14aba14e31c5530cda14b4792736da001246a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767243"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="c001d-103">Учебник. Создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c001d-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="c001d-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Майк Уоссон](https://github.com/mikewasson) (Mike Wasson)</span><span class="sxs-lookup"><span data-stu-id="c001d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="c001d-105">В этом учебнике приводятся основные сведения о создании веб-API с помощью ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c001d-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c001d-106">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="c001d-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c001d-107">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="c001d-107">Create a web API project.</span></span>
> * <span data-ttu-id="c001d-108">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="c001d-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c001d-109">Формирование шаблонов контроллера с использованием методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="c001d-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="c001d-110">Настройка маршрутизации, URL-пути и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="c001d-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="c001d-111">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="c001d-111">Call the web API with Postman.</span></span>

<span data-ttu-id="c001d-112">В итоге вы получите веб-API, позволяющий работать с элементами списка дел, хранимыми в базе данных.</span><span class="sxs-lookup"><span data-stu-id="c001d-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="c001d-113">Обзор</span><span class="sxs-lookup"><span data-stu-id="c001d-113">Overview</span></span>

<span data-ttu-id="c001d-114">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="c001d-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c001d-115">API</span><span class="sxs-lookup"><span data-stu-id="c001d-115">API</span></span> | <span data-ttu-id="c001d-116">Описание</span><span class="sxs-lookup"><span data-stu-id="c001d-116">Description</span></span> | <span data-ttu-id="c001d-117">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="c001d-117">Request body</span></span> | <span data-ttu-id="c001d-118">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="c001d-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="c001d-119">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="c001d-119">Get all to-do items</span></span> | <span data-ttu-id="c001d-120">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c001d-120">None</span></span> | <span data-ttu-id="c001d-121">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="c001d-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="c001d-122">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="c001d-122">Get an item by ID</span></span> | <span data-ttu-id="c001d-123">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c001d-123">None</span></span> | <span data-ttu-id="c001d-124">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c001d-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="c001d-125">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="c001d-125">Add a new item</span></span> | <span data-ttu-id="c001d-126">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c001d-126">To-do item</span></span> | <span data-ttu-id="c001d-127">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c001d-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="c001d-128">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c001d-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c001d-129">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c001d-129">To-do item</span></span> | <span data-ttu-id="c001d-130">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c001d-130">None</span></span> |
|<span data-ttu-id="c001d-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c001d-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="c001d-132">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c001d-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c001d-133">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c001d-133">None</span></span> | <span data-ttu-id="c001d-134">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c001d-134">None</span></span>|

<span data-ttu-id="c001d-135">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="c001d-135">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="c001d-141">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="c001d-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c001d-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c001d-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c001d-144">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="c001d-145">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="c001d-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c001d-147">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="c001d-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c001d-148">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c001d-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="c001d-149">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c001d-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="c001d-150">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="c001d-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="c001d-151">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c001d-151">Select the **API** template and click **Create**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c001d-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c001d-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c001d-154">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c001d-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c001d-155">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="c001d-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c001d-156">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c001d-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="c001d-157">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c001d-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="c001d-158">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="c001d-158">The preceding commands:</span></span>

  * <span data-ttu-id="c001d-159">Создает новый проект веб-API и открывает его в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c001d-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="c001d-160">Добавляет пакеты NuGet, которые понадобятся в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="c001d-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c001d-161">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c001d-162">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="c001d-162">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c001d-164">Щелкните **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c001d-164">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Диалоговое окно "Новый проект" в macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="c001d-166">В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **Целевой платформы** выберите \* *.NET Core 3.1*.</span><span class="sxs-lookup"><span data-stu-id="c001d-166">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="c001d-167">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c001d-167">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="c001d-169">Откройте командный терминал в папке проекта и выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c001d-169">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="c001d-170">Тестирование API</span><span class="sxs-lookup"><span data-stu-id="c001d-170">Test the API</span></span>

<span data-ttu-id="c001d-171">Шаблон проекта создает API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="c001d-171">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="c001d-172">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="c001d-172">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c001d-174">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c001d-174">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c001d-175">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/WeatherForecast`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="c001d-175">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="c001d-176">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c001d-176">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="c001d-177">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c001d-177">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c001d-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c001d-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c001d-179">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c001d-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c001d-180">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="c001d-180">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c001d-181">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c001d-182">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c001d-182">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c001d-183">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="c001d-183">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c001d-184">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="c001d-184">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c001d-185">Добавьте `/WeatherForecast` к URL-адресу (измените URL-адрес на `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="c001d-185">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="c001d-186">Возвращаемые данные JSON будут выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="c001d-186">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="c001d-187">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="c001d-187">Add a model class</span></span>

<span data-ttu-id="c001d-188">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="c001d-188">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c001d-189">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c001d-189">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c001d-191">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c001d-191">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="c001d-192">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="c001d-192">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c001d-193">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="c001d-193">Name the folder *Models*.</span></span>

* <span data-ttu-id="c001d-194">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="c001d-194">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c001d-195">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-195">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="c001d-196">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c001d-196">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c001d-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c001d-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c001d-198">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="c001d-198">Add a folder named *Models*.</span></span>

* <span data-ttu-id="c001d-199">Добавьте класс `TodoItem` в папку *Models*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="c001d-199">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c001d-200">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c001d-201">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c001d-201">Right-click the project.</span></span> <span data-ttu-id="c001d-202">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="c001d-202">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c001d-203">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="c001d-203">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c001d-205">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="c001d-205">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="c001d-206">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c001d-206">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="c001d-207">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c001d-207">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="c001d-208">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="c001d-208">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c001d-209">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models*.</span><span class="sxs-lookup"><span data-stu-id="c001d-209">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="c001d-210">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="c001d-210">Add a database context</span></span>

<span data-ttu-id="c001d-211">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="c001d-211">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c001d-212">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="c001d-212">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-213">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="c001d-214">Добавление Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="c001d-214">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="c001d-215">В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.</span><span class="sxs-lookup"><span data-stu-id="c001d-215">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="c001d-216">Перейдите на вкладку **Обзор** и введите **Microsoft.EntityFrameworkCore.SqlServer** в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="c001d-216">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="c001d-217">На панели слева выберите **Microsoft.EntityFrameworkCore.SqlServer**.</span><span class="sxs-lookup"><span data-stu-id="c001d-217">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="c001d-218">Установите флажок **Проект** на правой панели и выберите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-218">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="c001d-219">Добавьте пакет NuGet `Microsoft.EntityFrameworkCore.InMemory` согласно инструкциям выше.</span><span class="sxs-lookup"><span data-stu-id="c001d-219">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Диспетчер пакетов NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="c001d-221">Добавление контекста базы данных TodoContext</span><span class="sxs-lookup"><span data-stu-id="c001d-221">Add the TodoContext database context</span></span>

* <span data-ttu-id="c001d-222">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="c001d-222">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c001d-223">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-223">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c001d-224">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c001d-225">Добавьте класс `TodoContext` в папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="c001d-225">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="c001d-226">Введите следующий код:</span><span class="sxs-lookup"><span data-stu-id="c001d-226">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="c001d-227">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="c001d-227">Register the database context</span></span>

<span data-ttu-id="c001d-228">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c001d-228">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c001d-229">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="c001d-229">The container provides the service to controllers.</span></span>

<span data-ttu-id="c001d-230">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="c001d-230">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="c001d-231">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="c001d-231">The preceding code:</span></span>

* <span data-ttu-id="c001d-232">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="c001d-232">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c001d-233">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c001d-233">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c001d-234">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="c001d-234">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="c001d-235">Формирование шаблонов контроллера</span><span class="sxs-lookup"><span data-stu-id="c001d-235">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-236">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c001d-237">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c001d-237">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c001d-238">Щелкните **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="c001d-238">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c001d-239">Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-239">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="c001d-240">В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="c001d-240">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="c001d-241">Выберите **TodoItem (TodoApi.Models)** в поле **Класс модели**.</span><span class="sxs-lookup"><span data-stu-id="c001d-241">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="c001d-242">Выберите **TodoContext (TodoApi.Models)** в поле **Класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="c001d-242">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="c001d-243">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-243">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c001d-244">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-244">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c001d-245">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c001d-245">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="c001d-246">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="c001d-246">The preceding commands:</span></span>

* <span data-ttu-id="c001d-247">добавляют пакеты NuGet, необходимые для формирования шаблонов;</span><span class="sxs-lookup"><span data-stu-id="c001d-247">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="c001d-248">устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);</span><span class="sxs-lookup"><span data-stu-id="c001d-248">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="c001d-249">формируют шаблоны для `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="c001d-249">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="c001d-250">Сформированный код:</span><span class="sxs-lookup"><span data-stu-id="c001d-250">The generated code:</span></span>

* <span data-ttu-id="c001d-251">Пометьте этот класс атрибутом [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="c001d-251">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="c001d-252">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="c001d-252">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c001d-253">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="c001d-253">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c001d-254">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="c001d-254">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c001d-255">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="c001d-255">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="c001d-256">Шаблоны ASP.NET Core для:</span><span class="sxs-lookup"><span data-stu-id="c001d-256">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="c001d-257">Контроллеры с представлениями включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="c001d-257">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="c001d-258">Контроллеры API не включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="c001d-258">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="c001d-259">Если токен `[action]` не находится в шаблоне маршрута, имя [действия](xref:mvc/controllers/routing#action) исключается из маршрута.</span><span class="sxs-lookup"><span data-stu-id="c001d-259">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="c001d-260">То есть имя связанного метода действия не используется в соответствующем маршруте.</span><span class="sxs-lookup"><span data-stu-id="c001d-260">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="c001d-261">Знакомство с методом создания PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="c001d-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="c001d-262">Измените инструкцию возврата в `PostTodoItem` и используйте оператор [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="c001d-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="c001d-263">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="c001d-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="c001d-264">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="c001d-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c001d-265">Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="c001d-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="c001d-266">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="c001d-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="c001d-267">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="c001d-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c001d-268">Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location).</span><span class="sxs-lookup"><span data-stu-id="c001d-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="c001d-269">Заголовок `Location` указывает [URI](https://developer.mozilla.org/docs/Glossary/URI) новой созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="c001d-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="c001d-270">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c001d-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c001d-271">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="c001d-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c001d-272">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="c001d-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="c001d-273">Установка Postman</span><span class="sxs-lookup"><span data-stu-id="c001d-273">Install Postman</span></span>

<span data-ttu-id="c001d-274">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="c001d-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c001d-275">Установка [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="c001d-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="c001d-276">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="c001d-276">Start the web app.</span></span>
* <span data-ttu-id="c001d-277">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="c001d-277">Start Postman.</span></span>
* <span data-ttu-id="c001d-278">Отключение параметра **Проверка SSL-сертификата**</span><span class="sxs-lookup"><span data-stu-id="c001d-278">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="c001d-279">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="c001d-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="c001d-280">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="c001d-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="c001d-281">Тестирование PostTodoItem с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="c001d-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="c001d-282">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="c001d-282">Create a new request.</span></span>
* <span data-ttu-id="c001d-283">Установите HTTP-метод `POST`.</span><span class="sxs-lookup"><span data-stu-id="c001d-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c001d-284">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="c001d-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="c001d-285">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="c001d-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c001d-286">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="c001d-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="c001d-287">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="c001d-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c001d-288">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-288">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="c001d-290">Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="c001d-290">Test the location header URI</span></span>

* <span data-ttu-id="c001d-291">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="c001d-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c001d-292">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="c001d-292">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="c001d-294">Укажите метод GET.</span><span class="sxs-lookup"><span data-stu-id="c001d-294">Set the method to GET.</span></span>
* <span data-ttu-id="c001d-295">Вставьте URI (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="c001d-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="c001d-296">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="c001d-297">Знакомство с методами GET</span><span class="sxs-lookup"><span data-stu-id="c001d-297">Examine the GET methods</span></span>

<span data-ttu-id="c001d-298">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="c001d-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="c001d-299">Протестируйте приложение, вызвав эти две конечные точки в браузере или в Postman.</span><span class="sxs-lookup"><span data-stu-id="c001d-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="c001d-300">Пример:</span><span class="sxs-lookup"><span data-stu-id="c001d-300">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="c001d-301">При вызове `GetTodoItems` возвращается примерно такой ответ:</span><span class="sxs-lookup"><span data-stu-id="c001d-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="c001d-302">Тестирование Get с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="c001d-302">Test Get with Postman</span></span>

* <span data-ttu-id="c001d-303">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="c001d-303">Create a new request.</span></span>
* <span data-ttu-id="c001d-304">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="c001d-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="c001d-305">Укажите URL-адрес запроса `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c001d-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c001d-306">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c001d-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c001d-307">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="c001d-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c001d-308">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-308">Select **Send**.</span></span>

<span data-ttu-id="c001d-309">Это приложение использует выполняющуюся в памяти базу данных.</span><span class="sxs-lookup"><span data-stu-id="c001d-309">This app uses an in-memory database.</span></span> <span data-ttu-id="c001d-310">Если остановить и вновь запустить его, предшествующий запрос GET не возвратит никаких данных.</span><span class="sxs-lookup"><span data-stu-id="c001d-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="c001d-311">Если данные не возвращаются, данные для приложения получаются методом [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="c001d-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="c001d-312">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="c001d-312">Routing and URL paths</span></span>

<span data-ttu-id="c001d-313">Атрибут [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="c001d-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c001d-314">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c001d-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c001d-315">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="c001d-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="c001d-316">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="c001d-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c001d-317">В этом примере класс контроллера имеет имя **TodoItems**, а сам контроллер, соответственно, — "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="c001d-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="c001d-318">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="c001d-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c001d-319">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="c001d-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c001d-320">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="c001d-320">This sample doesn't use a template.</span></span> <span data-ttu-id="c001d-321">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c001d-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c001d-322">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="c001d-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c001d-323">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="c001d-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="c001d-324">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="c001d-324">Return values</span></span>

<span data-ttu-id="c001d-325">Возвращаемое значение имеет тип `GetTodoItems`, а метод `GetTodoItem` имеет тип [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="c001d-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c001d-326">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="c001d-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c001d-327">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="c001d-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c001d-328">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="c001d-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c001d-329">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="c001d-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c001d-330">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="c001d-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c001d-331">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 ([Не найдено](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)).</span><span class="sxs-lookup"><span data-stu-id="c001d-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="c001d-332">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="c001d-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c001d-333">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="c001d-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="c001d-334">Метод PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="c001d-334">The PutTodoItem method</span></span>

<span data-ttu-id="c001d-335">Проверьте метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c001d-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="c001d-336">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="c001d-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c001d-337">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c001d-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c001d-338">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="c001d-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c001d-339">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="c001d-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c001d-340">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="c001d-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="c001d-341">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="c001d-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="c001d-342">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="c001d-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c001d-343">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="c001d-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c001d-344">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="c001d-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c001d-345">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="c001d-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c001d-346">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="c001d-346">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="c001d-348">Метод DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="c001d-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="c001d-349">Проверьте метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c001d-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="c001d-350">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="c001d-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="c001d-351">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="c001d-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c001d-352">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="c001d-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c001d-353">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="c001d-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="c001d-354">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-354">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="c001d-355">Предотвращение избыточной публикации</span><span class="sxs-lookup"><span data-stu-id="c001d-355">Prevent over-posting</span></span>

<span data-ttu-id="c001d-356">В настоящее время пример приложения предоставляет весь объект `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c001d-356">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="c001d-357">Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели.</span><span class="sxs-lookup"><span data-stu-id="c001d-357">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="c001d-358">Это связано с несколькими причинами, и безопасность является основной.</span><span class="sxs-lookup"><span data-stu-id="c001d-358">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="c001d-359">Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления.</span><span class="sxs-lookup"><span data-stu-id="c001d-359">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="c001d-360">В этой статье используется **DTO**.</span><span class="sxs-lookup"><span data-stu-id="c001d-360">**DTO** is used in this article.</span></span>

<span data-ttu-id="c001d-361">DTO можно использовать для следующего:</span><span class="sxs-lookup"><span data-stu-id="c001d-361">A DTO may be used to:</span></span>

* <span data-ttu-id="c001d-362">Предотвращение избыточной публикации.</span><span class="sxs-lookup"><span data-stu-id="c001d-362">Prevent over-posting.</span></span>
* <span data-ttu-id="c001d-363">Скрытие свойств, которые не предназначены для просмотра клиентами.</span><span class="sxs-lookup"><span data-stu-id="c001d-363">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="c001d-364">Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="c001d-364">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="c001d-365">Сведение графов объектов, содержащих вложенные объекты.</span><span class="sxs-lookup"><span data-stu-id="c001d-365">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="c001d-366">Сведенные графы объектов могут быть удобнее для клиентов.</span><span class="sxs-lookup"><span data-stu-id="c001d-366">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="c001d-367">Чтобы продемонстрировать подход с применением DTO, обновите класс `TodoItem`, включив в него поле секрета:</span><span class="sxs-lookup"><span data-stu-id="c001d-367">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="c001d-368">Поле секрета должно быть скрыто в этом приложении, однако административное приложение может отобразить его.</span><span class="sxs-lookup"><span data-stu-id="c001d-368">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="c001d-369">Убедитесь, что вы можете отправить и получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="c001d-369">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="c001d-370">Создайте модель DTO:</span><span class="sxs-lookup"><span data-stu-id="c001d-370">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="c001d-371">Обновите `TodoItemsController` для использования `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="c001d-371">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="c001d-372">Убедитесь, что вы можете отправить или получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="c001d-372">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="c001d-373">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="c001d-373">Call the web API with JavaScript</span></span>

<span data-ttu-id="c001d-374">См. руководство по [: Вызовите веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="c001d-374">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c001d-375">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="c001d-375">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c001d-376">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="c001d-376">Create a web API project.</span></span>
> * <span data-ttu-id="c001d-377">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="c001d-377">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c001d-378">Добавление контроллера.</span><span class="sxs-lookup"><span data-stu-id="c001d-378">Add a controller.</span></span>
> * <span data-ttu-id="c001d-379">Добавление методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="c001d-379">Add CRUD methods.</span></span>
> * <span data-ttu-id="c001d-380">Настройка маршрутизации и путей URL.</span><span class="sxs-lookup"><span data-stu-id="c001d-380">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="c001d-381">Указание возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="c001d-381">Specify return values.</span></span>
> * <span data-ttu-id="c001d-382">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="c001d-382">Call the web API with Postman.</span></span>
> * <span data-ttu-id="c001d-383">Вызовите веб-API с помощью JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c001d-383">Call the web API with JavaScript.</span></span>

<span data-ttu-id="c001d-384">В конечном итоге вы получите веб-API, обеспечивающий управление элементами списка дел, хранящимися в реляционной базе данных.</span><span class="sxs-lookup"><span data-stu-id="c001d-384">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="c001d-385">Обзор</span><span class="sxs-lookup"><span data-stu-id="c001d-385">Overview</span></span>

<span data-ttu-id="c001d-386">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="c001d-386">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c001d-387">API</span><span class="sxs-lookup"><span data-stu-id="c001d-387">API</span></span> | <span data-ttu-id="c001d-388">Описание</span><span class="sxs-lookup"><span data-stu-id="c001d-388">Description</span></span> | <span data-ttu-id="c001d-389">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="c001d-389">Request body</span></span> | <span data-ttu-id="c001d-390">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="c001d-390">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="c001d-391">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="c001d-391">GET /api/TodoItems</span></span> | <span data-ttu-id="c001d-392">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="c001d-392">Get all to-do items</span></span> | <span data-ttu-id="c001d-393">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c001d-393">None</span></span> | <span data-ttu-id="c001d-394">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="c001d-394">Array of to-do items</span></span>|
|<span data-ttu-id="c001d-395">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="c001d-395">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="c001d-396">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="c001d-396">Get an item by ID</span></span> | <span data-ttu-id="c001d-397">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c001d-397">None</span></span> | <span data-ttu-id="c001d-398">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c001d-398">To-do item</span></span>|
|<span data-ttu-id="c001d-399">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="c001d-399">POST /api/TodoItems</span></span> | <span data-ttu-id="c001d-400">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="c001d-400">Add a new item</span></span> | <span data-ttu-id="c001d-401">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c001d-401">To-do item</span></span> | <span data-ttu-id="c001d-402">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c001d-402">To-do item</span></span> |
|<span data-ttu-id="c001d-403">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="c001d-403">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="c001d-404">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c001d-404">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c001d-405">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c001d-405">To-do item</span></span> | <span data-ttu-id="c001d-406">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c001d-406">None</span></span> |
|<span data-ttu-id="c001d-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c001d-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="c001d-408">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c001d-408">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c001d-409">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c001d-409">None</span></span> | <span data-ttu-id="c001d-410">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c001d-410">None</span></span>|

<span data-ttu-id="c001d-411">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="c001d-411">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="c001d-417">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="c001d-417">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-418">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c001d-419">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c001d-419">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c001d-420">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="c001d-421">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="c001d-421">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c001d-423">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="c001d-423">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c001d-424">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c001d-424">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="c001d-425">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c001d-425">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="c001d-426">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="c001d-426">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="c001d-427">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c001d-427">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="c001d-428">**Не** выбирайте **Включение поддержки Docker**.</span><span class="sxs-lookup"><span data-stu-id="c001d-428">**Don't** select **Enable Docker Support**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c001d-430">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c001d-430">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c001d-431">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c001d-431">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c001d-432">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="c001d-432">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c001d-433">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c001d-433">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="c001d-434">С помощью этих команд создается новый проект веб-API и открывается новый экземпляр Visual Studio Code в новой папке проекта.</span><span class="sxs-lookup"><span data-stu-id="c001d-434">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="c001d-435">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c001d-435">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c001d-436">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c001d-437">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="c001d-437">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c001d-439">Щелкните **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c001d-439">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Диалоговое окно "Новый проект" в macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="c001d-441">В диалоговом окне **Настройка нового веб-API ASP.NET Core** оставьте установленное по умолчанию значение для параметра **Целевая платформа**, то есть \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="c001d-441">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="c001d-442">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c001d-442">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="c001d-444">Тестирование API</span><span class="sxs-lookup"><span data-stu-id="c001d-444">Test the API</span></span>

<span data-ttu-id="c001d-445">Шаблон проекта создает API `values`.</span><span class="sxs-lookup"><span data-stu-id="c001d-445">The project template creates a `values` API.</span></span> <span data-ttu-id="c001d-446">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="c001d-446">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-447">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c001d-448">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c001d-448">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c001d-449">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/api/values`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="c001d-449">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="c001d-450">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c001d-450">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="c001d-451">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c001d-451">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c001d-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c001d-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c001d-453">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c001d-453">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c001d-454">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="c001d-454">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c001d-455">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c001d-456">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c001d-456">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c001d-457">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="c001d-457">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c001d-458">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="c001d-458">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c001d-459">Добавьте `/api/values` к URL-адресу (измените URL-адрес на `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="c001d-459">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="c001d-460">Возвращается следующий файл JSON:</span><span class="sxs-lookup"><span data-stu-id="c001d-460">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="c001d-461">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="c001d-461">Add a model class</span></span>

<span data-ttu-id="c001d-462">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="c001d-462">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c001d-463">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c001d-463">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-464">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c001d-465">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c001d-465">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="c001d-466">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="c001d-466">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c001d-467">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="c001d-467">Name the folder *Models*.</span></span>

* <span data-ttu-id="c001d-468">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="c001d-468">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c001d-469">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-469">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="c001d-470">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c001d-470">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c001d-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c001d-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c001d-472">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="c001d-472">Add a folder named *Models*.</span></span>

* <span data-ttu-id="c001d-473">Добавьте класс `TodoItem` в папку *Models*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="c001d-473">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c001d-474">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c001d-475">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c001d-475">Right-click the project.</span></span> <span data-ttu-id="c001d-476">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="c001d-476">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c001d-477">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="c001d-477">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c001d-479">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="c001d-479">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="c001d-480">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c001d-480">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="c001d-481">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c001d-481">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="c001d-482">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="c001d-482">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c001d-483">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models*.</span><span class="sxs-lookup"><span data-stu-id="c001d-483">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="c001d-484">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="c001d-484">Add a database context</span></span>

<span data-ttu-id="c001d-485">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="c001d-485">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c001d-486">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="c001d-486">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c001d-488">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="c001d-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c001d-489">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-489">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c001d-490">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c001d-491">Добавьте класс `TodoContext` в папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="c001d-491">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="c001d-492">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c001d-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="c001d-493">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="c001d-493">Register the database context</span></span>

<span data-ttu-id="c001d-494">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c001d-494">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c001d-495">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="c001d-495">The container provides the service to controllers.</span></span>

<span data-ttu-id="c001d-496">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="c001d-496">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="c001d-497">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="c001d-497">The preceding code:</span></span>

* <span data-ttu-id="c001d-498">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="c001d-498">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c001d-499">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c001d-499">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c001d-500">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="c001d-500">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="c001d-501">Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="c001d-501">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-502">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-502">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c001d-503">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c001d-503">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c001d-504">Щелкните **Добавить** > **Создать элемент**.</span><span class="sxs-lookup"><span data-stu-id="c001d-504">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="c001d-505">В диалоговом окне **Добавить новый элемент** выберите шаблон **Класс контроллера API**.</span><span class="sxs-lookup"><span data-stu-id="c001d-505">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="c001d-506">Присвойте классу имя *TodoController* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-506">Name the class *TodoController*, and select **Add**.</span></span>

  ![Диалоговое окно добавления элемента с контроллером в поле поиска и выбранным контроллером веб-API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c001d-508">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-508">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c001d-509">В папке *Controllers* создайте класс с именем `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="c001d-509">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="c001d-510">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c001d-510">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="c001d-511">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="c001d-511">The preceding code:</span></span>

* <span data-ttu-id="c001d-512">Определяет класс контроллера API без методов.</span><span class="sxs-lookup"><span data-stu-id="c001d-512">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="c001d-513">Пометьте этот класс атрибутом [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="c001d-513">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="c001d-514">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="c001d-514">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c001d-515">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="c001d-515">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c001d-516">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="c001d-516">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c001d-517">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="c001d-517">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="c001d-518">Добавляет элемент `Item1` в базу данных, если она пуста.</span><span class="sxs-lookup"><span data-stu-id="c001d-518">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="c001d-519">Этот код находится в конструкторе и выполняется каждый раз при обнаружении нового HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="c001d-519">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="c001d-520">Если вы удалите все элементы, конструктор создаст `Item1` при следующем вызове метода API.</span><span class="sxs-lookup"><span data-stu-id="c001d-520">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="c001d-521">Поэтому может создаться впечатление, что удаление не было выполнено, хотя это не так.</span><span class="sxs-lookup"><span data-stu-id="c001d-521">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="c001d-522">Добавление методов Get</span><span class="sxs-lookup"><span data-stu-id="c001d-522">Add Get methods</span></span>

<span data-ttu-id="c001d-523">Чтобы получить API, который извлекает элементы списка дел, добавьте следующие методы в класс `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="c001d-523">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="c001d-524">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="c001d-524">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="c001d-525">Если приложение все еще выполняется, оно останавливается.</span><span class="sxs-lookup"><span data-stu-id="c001d-525">Stop the app if it's still running.</span></span> <span data-ttu-id="c001d-526">Затем оно запускается снова с последними изменениями.</span><span class="sxs-lookup"><span data-stu-id="c001d-526">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="c001d-527">Протестируйте приложение, вызвав эти две конечные точки в браузере.</span><span class="sxs-lookup"><span data-stu-id="c001d-527">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="c001d-528">Пример:</span><span class="sxs-lookup"><span data-stu-id="c001d-528">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="c001d-529">При вызове `GetTodoItems` возвращается следующий ответ HTTP:</span><span class="sxs-lookup"><span data-stu-id="c001d-529">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="c001d-530">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="c001d-530">Routing and URL paths</span></span>

<span data-ttu-id="c001d-531">Атрибут [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="c001d-531">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c001d-532">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c001d-532">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c001d-533">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="c001d-533">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="c001d-534">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="c001d-534">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c001d-535">В этом примере класс контроллера носит имя **Todo**, а сам контроллер, соответственно, — "todo".</span><span class="sxs-lookup"><span data-stu-id="c001d-535">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="c001d-536">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="c001d-536">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c001d-537">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="c001d-537">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c001d-538">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="c001d-538">This sample doesn't use a template.</span></span> <span data-ttu-id="c001d-539">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c001d-539">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c001d-540">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="c001d-540">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c001d-541">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="c001d-541">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="c001d-542">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="c001d-542">Return values</span></span>

<span data-ttu-id="c001d-543">Возвращаемое значение имеет тип `GetTodoItems`, а метод `GetTodoItem` имеет тип [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="c001d-543">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c001d-544">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="c001d-544">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c001d-545">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="c001d-545">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c001d-546">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="c001d-546">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c001d-547">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="c001d-547">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c001d-548">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="c001d-548">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c001d-549">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 ([Не найдено](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)).</span><span class="sxs-lookup"><span data-stu-id="c001d-549">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="c001d-550">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="c001d-550">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c001d-551">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="c001d-551">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="c001d-552">Тестирование метода GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="c001d-552">Test the GetTodoItems method</span></span>

<span data-ttu-id="c001d-553">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="c001d-553">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c001d-554">Установите [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="c001d-554">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="c001d-555">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="c001d-555">Start the web app.</span></span>
* <span data-ttu-id="c001d-556">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="c001d-556">Start Postman.</span></span>
* <span data-ttu-id="c001d-557">Отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="c001d-557">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c001d-558">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c001d-558">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c001d-559">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="c001d-559">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c001d-560">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c001d-560">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c001d-561">В меню **Postman** > **Настройки** (вкладка **Общие**) отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="c001d-561">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="c001d-562">Кроме того, можно выбрать значок гаечного ключа и выбрать **Параметры**, а затем отключить проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="c001d-562">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="c001d-563">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="c001d-563">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="c001d-564">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="c001d-564">Create a new request.</span></span>
  * <span data-ttu-id="c001d-565">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="c001d-565">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="c001d-566">Укажите URL-адрес запроса `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="c001d-566">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="c001d-567">Например, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="c001d-567">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="c001d-568">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="c001d-568">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c001d-569">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-569">Select **Send**.</span></span>

![Postman с запросом Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="c001d-571">Добавление метода Create</span><span class="sxs-lookup"><span data-stu-id="c001d-571">Add a Create method</span></span>

<span data-ttu-id="c001d-572">Добавьте следующий метод `PostTodoItem` в *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="c001d-572">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="c001d-573">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="c001d-573">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="c001d-574">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="c001d-574">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c001d-575">Метод `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="c001d-575">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="c001d-576">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="c001d-576">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="c001d-577">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="c001d-577">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c001d-578">Добавляет заголовок `Location` в ответ.</span><span class="sxs-lookup"><span data-stu-id="c001d-578">Adds a `Location` header to the response.</span></span> <span data-ttu-id="c001d-579">Заголовок `Location` расположения указывает URI вновь созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="c001d-579">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="c001d-580">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c001d-580">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c001d-581">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="c001d-581">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c001d-582">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="c001d-582">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="c001d-583">Тестирование метода PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="c001d-583">Test the PostTodoItem method</span></span>

* <span data-ttu-id="c001d-584">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="c001d-584">Build the project.</span></span>
* <span data-ttu-id="c001d-585">В Postman укажите метод HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="c001d-585">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c001d-586">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="c001d-586">Select the **Body** tab.</span></span>
* <span data-ttu-id="c001d-587">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="c001d-587">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c001d-588">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="c001d-588">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="c001d-589">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="c001d-589">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c001d-590">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-590">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/create.png)

  <span data-ttu-id="c001d-592">Если вы получаете ошибку 405 "Недопустимый метод", это может свидетельствовать о том, что после добавления метода `PostTodoItem` не была выполнена компиляция проекта.</span><span class="sxs-lookup"><span data-stu-id="c001d-592">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="c001d-593">Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="c001d-593">Test the location header URI</span></span>

* <span data-ttu-id="c001d-594">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="c001d-594">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c001d-595">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="c001d-595">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="c001d-597">Укажите метод GET.</span><span class="sxs-lookup"><span data-stu-id="c001d-597">Set the method to GET.</span></span>
* <span data-ttu-id="c001d-598">Вставьте URI (например, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="c001d-598">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="c001d-599">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-599">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="c001d-600">Добавление метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="c001d-600">Add a PutTodoItem method</span></span>

<span data-ttu-id="c001d-601">Добавьте приведенный ниже метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c001d-601">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="c001d-602">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="c001d-602">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c001d-603">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c001d-603">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c001d-604">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="c001d-604">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c001d-605">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="c001d-605">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c001d-606">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="c001d-606">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="c001d-607">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="c001d-607">Test the PutTodoItem method</span></span>

<span data-ttu-id="c001d-608">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="c001d-608">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c001d-609">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="c001d-609">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c001d-610">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="c001d-610">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c001d-611">Обновите элемент списка дел с идентификатором = 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="c001d-611">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c001d-612">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="c001d-612">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="c001d-614">Добавление метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="c001d-614">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="c001d-615">Добавьте приведенный ниже метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c001d-615">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="c001d-616">`DeleteTodoItem`Ответ[ — 204 (нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c001d-616">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="c001d-617">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="c001d-617">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="c001d-618">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="c001d-618">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c001d-619">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="c001d-619">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c001d-620">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="c001d-620">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="c001d-621">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c001d-621">Select **Send**.</span></span>

<span data-ttu-id="c001d-622">В этом примере приложения вы можете удалить все элементы.</span><span class="sxs-lookup"><span data-stu-id="c001d-622">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="c001d-623">Однако в случае удаления последнего элемента в момент следующего вызова API конструктор класса модели создаст новый элемент.</span><span class="sxs-lookup"><span data-stu-id="c001d-623">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="c001d-624">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="c001d-624">Call the web API with JavaScript</span></span>

<span data-ttu-id="c001d-625">В этом разделе описано, как добавить HTML-страницу, которая использует JavaScript для вызова веб-API.</span><span class="sxs-lookup"><span data-stu-id="c001d-625">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="c001d-626">jQuery инициирует запрос.</span><span class="sxs-lookup"><span data-stu-id="c001d-626">jQuery initiates the request.</span></span> <span data-ttu-id="c001d-627">JavaScript изменяет страницу, используя сведения из ответа API.</span><span class="sxs-lookup"><span data-stu-id="c001d-627">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="c001d-628">Настройте приложение для [обслуживания статических файлов](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) и [включения сопоставления файлов по умолчанию](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), обновив *Startup.cs* следующим выделенным кодом:</span><span class="sxs-lookup"><span data-stu-id="c001d-628">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="c001d-629">Создайте папку *wwwroot* в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="c001d-629">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="c001d-630">Добавьте HTML-файл *index.html* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="c001d-630">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="c001d-631">Замените его содержимое следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="c001d-631">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="c001d-632">Добавьте файл JavaScript с именем *site.js* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="c001d-632">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="c001d-633">Замените его содержимое следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c001d-633">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="c001d-634">Может потребоваться изменение параметров запуска проекта ASP.NET Core для локального тестирования HTML-страницы:</span><span class="sxs-lookup"><span data-stu-id="c001d-634">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="c001d-635">Откройте файл *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c001d-635">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="c001d-636">Удалите свойство `launchUrl`, чтобы приложение открылось через *index.html* &mdash; файл проекта по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c001d-636">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="c001d-637">В этом примере вызываются все методы CRUD в веб-API.</span><span class="sxs-lookup"><span data-stu-id="c001d-637">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="c001d-638">Ниже приводится пояснение вызовов API.</span><span class="sxs-lookup"><span data-stu-id="c001d-638">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="c001d-639">Получение списка элементов задач</span><span class="sxs-lookup"><span data-stu-id="c001d-639">Get a list of to-do items</span></span>

<span data-ttu-id="c001d-640">jQuery отправляет запрос HTTP GET к веб-API, который возвращает ответ JSON, представляющий массив элементов списка дел.</span><span class="sxs-lookup"><span data-stu-id="c001d-640">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="c001d-641">В случае успешного запроса используется функция обратного вызова `success`.</span><span class="sxs-lookup"><span data-stu-id="c001d-641">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="c001d-642">При обратном вызове в модель DOM вносятся данные о задачах.</span><span class="sxs-lookup"><span data-stu-id="c001d-642">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="c001d-643">Добавление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="c001d-643">Add a to-do item</span></span>

<span data-ttu-id="c001d-644">jQuery отправляет запрос HTTP POST с элементом списка дел в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="c001d-644">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="c001d-645">Для параметров `accepts` и `contentType` указывается `application/json`, чтобы указать тип носителя при получении и отправке.</span><span class="sxs-lookup"><span data-stu-id="c001d-645">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="c001d-646">Элемент списка дел преобразуется в JSON с помощью [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="c001d-646">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="c001d-647">Если интерфейс API возвращает код состояния успешного выполнения, вызывается функция `getData` для обновления HTML-таблицы.</span><span class="sxs-lookup"><span data-stu-id="c001d-647">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="c001d-648">Обновление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="c001d-648">Update a to-do item</span></span>

<span data-ttu-id="c001d-649">Обновление элемента списка дел выполняется аналогично его добавлению.</span><span class="sxs-lookup"><span data-stu-id="c001d-649">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="c001d-650">`url` изменяется, чтобы добавить уникальный идентификатор для элемента, а в качестве `type` устанавливается `PUT`.</span><span class="sxs-lookup"><span data-stu-id="c001d-650">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="c001d-651">Удаление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="c001d-651">Delete a to-do item</span></span>

<span data-ttu-id="c001d-652">Чтобы удалить элемент списка дел, установите для `type` в вызове AJAX значение `DELETE` и укажите уникальный идентификатор элемента в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="c001d-652">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="c001d-653">Добавление поддержки аутентификации в веб-API</span><span class="sxs-lookup"><span data-stu-id="c001d-653">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="c001d-654">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c001d-654">Additional resources</span></span>

<span data-ttu-id="c001d-655">[Просмотреть или скачать пример кода для этого учебника](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="c001d-655">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="c001d-656">См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="c001d-656">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="c001d-657">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="c001d-657">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="c001d-658">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="c001d-658">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
