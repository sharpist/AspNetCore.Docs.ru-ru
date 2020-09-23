---
title: Учебник. Создание веб-API с помощью ASP.NET Core
author: rick-anderson
description: Узнайте, как создать веб-API с помощью ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: ebce9f2f4992d83c6b28edb5c771cdfc8a7a0b6a
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080385"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="afd51-103">Учебник. Создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="afd51-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="afd51-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Майк Уоссон](https://github.com/mikewasson) (Mike Wasson)</span><span class="sxs-lookup"><span data-stu-id="afd51-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="afd51-105">В этом учебнике приводятся основные сведения о создании веб-API с помощью ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="afd51-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="afd51-106">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="afd51-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="afd51-107">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="afd51-107">Create a web API project.</span></span>
> * <span data-ttu-id="afd51-108">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="afd51-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="afd51-109">Формирование шаблонов контроллера с использованием методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="afd51-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="afd51-110">Настройка маршрутизации, URL-пути и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="afd51-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="afd51-111">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="afd51-111">Call the web API with Postman.</span></span>

<span data-ttu-id="afd51-112">В итоге вы получите веб-API, позволяющий работать с элементами списка дел, хранимыми в базе данных.</span><span class="sxs-lookup"><span data-stu-id="afd51-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="afd51-113">Обзор</span><span class="sxs-lookup"><span data-stu-id="afd51-113">Overview</span></span>

<span data-ttu-id="afd51-114">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="afd51-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="afd51-115">API</span><span class="sxs-lookup"><span data-stu-id="afd51-115">API</span></span> | <span data-ttu-id="afd51-116">Описание</span><span class="sxs-lookup"><span data-stu-id="afd51-116">Description</span></span> | <span data-ttu-id="afd51-117">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="afd51-117">Request body</span></span> | <span data-ttu-id="afd51-118">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="afd51-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="afd51-119">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="afd51-119">Get all to-do items</span></span> | <span data-ttu-id="afd51-120">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="afd51-120">None</span></span> | <span data-ttu-id="afd51-121">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="afd51-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="afd51-122">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="afd51-122">Get an item by ID</span></span> | <span data-ttu-id="afd51-123">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="afd51-123">None</span></span> | <span data-ttu-id="afd51-124">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="afd51-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="afd51-125">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="afd51-125">Add a new item</span></span> | <span data-ttu-id="afd51-126">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="afd51-126">To-do item</span></span> | <span data-ttu-id="afd51-127">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="afd51-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="afd51-128">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="afd51-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="afd51-129">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="afd51-129">To-do item</span></span> | <span data-ttu-id="afd51-130">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="afd51-130">None</span></span> |
|<span data-ttu-id="afd51-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="afd51-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="afd51-132">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="afd51-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="afd51-133">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="afd51-133">None</span></span> | <span data-ttu-id="afd51-134">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="afd51-134">None</span></span>|

<span data-ttu-id="afd51-135">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="afd51-135">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="afd51-141">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="afd51-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="afd51-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="afd51-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="afd51-144">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="afd51-145">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="afd51-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="afd51-147">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="afd51-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="afd51-148">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afd51-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="afd51-149">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="afd51-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="afd51-150">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="afd51-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="afd51-151">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="afd51-151">Select the **API** template and click **Create**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="afd51-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="afd51-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="afd51-154">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="afd51-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="afd51-155">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="afd51-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="afd51-156">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="afd51-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="afd51-157">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="afd51-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="afd51-158">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="afd51-158">The preceding commands:</span></span>

  * <span data-ttu-id="afd51-159">Создает новый проект веб-API и открывает его в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="afd51-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="afd51-160">Добавляет пакеты NuGet, которые понадобятся в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="afd51-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="afd51-161">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="afd51-162">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="afd51-162">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="afd51-164">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afd51-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="afd51-165">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afd51-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Выбор шаблона API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="afd51-167">В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **целевой платформы** выберите последнюю версию .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="afd51-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="afd51-168">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afd51-168">Select **Next**.</span></span>

* <span data-ttu-id="afd51-169">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="afd51-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="afd51-171">Откройте командный терминал в папке проекта и выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="afd51-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="afd51-172">Тестирование API</span><span class="sxs-lookup"><span data-stu-id="afd51-172">Test the API</span></span>

<span data-ttu-id="afd51-173">Шаблон проекта создает API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="afd51-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="afd51-174">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="afd51-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="afd51-176">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="afd51-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="afd51-177">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/WeatherForecast`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="afd51-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="afd51-178">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="afd51-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="afd51-179">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="afd51-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="afd51-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="afd51-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="afd51-181">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="afd51-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="afd51-182">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="afd51-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="afd51-183">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="afd51-184">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="afd51-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="afd51-185">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="afd51-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="afd51-186">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="afd51-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="afd51-187">Добавьте `/WeatherForecast` к URL-адресу (измените URL-адрес на `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="afd51-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="afd51-188">Возвращаемые данные JSON будут выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="afd51-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="afd51-189">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="afd51-189">Add a model class</span></span>

<span data-ttu-id="afd51-190">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="afd51-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="afd51-191">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="afd51-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="afd51-193">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="afd51-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="afd51-194">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="afd51-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="afd51-195">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="afd51-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="afd51-196">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="afd51-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="afd51-197">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="afd51-198">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="afd51-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="afd51-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="afd51-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="afd51-200">Добавьте папку с именем *Models* .</span><span class="sxs-lookup"><span data-stu-id="afd51-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="afd51-201">Добавьте в папку *Models* класс `TodoItem` со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="afd51-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="afd51-202">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="afd51-203">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="afd51-203">Right-click the project.</span></span> <span data-ttu-id="afd51-204">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="afd51-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="afd51-205">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="afd51-205">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="afd51-207">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="afd51-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="afd51-208">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="afd51-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="afd51-209">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="afd51-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="afd51-210">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="afd51-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="afd51-211">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .</span><span class="sxs-lookup"><span data-stu-id="afd51-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="afd51-212">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="afd51-212">Add a database context</span></span>

<span data-ttu-id="afd51-213">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="afd51-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="afd51-214">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="afd51-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="afd51-216">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="afd51-216">Add NuGet packages</span></span>

* <span data-ttu-id="afd51-217">В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.</span><span class="sxs-lookup"><span data-stu-id="afd51-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="afd51-218">Перейдите на вкладку **Обзор** и введите **Microsoft.EntityFrameworkCore.SqlServer** в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="afd51-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="afd51-219">На панели слева выберите **Microsoft.EntityFrameworkCore.SqlServer**.</span><span class="sxs-lookup"><span data-stu-id="afd51-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="afd51-220">Установите флажок **Проект** на правой панели и выберите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="afd51-221">Добавьте пакет NuGet **пакет NuGet Microsoft.EntityFrameworkCore.InMemory**, выполнив приведенные выше инструкции.</span><span class="sxs-lookup"><span data-stu-id="afd51-221">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Диспетчер пакетов NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="afd51-223">Добавление контекста базы данных TodoContext</span><span class="sxs-lookup"><span data-stu-id="afd51-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="afd51-224">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="afd51-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="afd51-225">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="afd51-226">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="afd51-227">Добавьте класс `TodoContext` в папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="afd51-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="afd51-228">Введите следующий код:</span><span class="sxs-lookup"><span data-stu-id="afd51-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="afd51-229">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="afd51-229">Register the database context</span></span>

<span data-ttu-id="afd51-230">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="afd51-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="afd51-231">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="afd51-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="afd51-232">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="afd51-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="afd51-233">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="afd51-233">The preceding code:</span></span>

* <span data-ttu-id="afd51-234">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="afd51-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="afd51-235">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="afd51-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="afd51-236">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="afd51-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="afd51-237">Формирование шаблонов контроллера</span><span class="sxs-lookup"><span data-stu-id="afd51-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="afd51-239">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="afd51-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="afd51-240">Щелкните **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="afd51-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="afd51-241">Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="afd51-242">В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="afd51-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="afd51-243">Выберите **TodoItem (TodoApi.Models)** в поле **Класс модели**.</span><span class="sxs-lookup"><span data-stu-id="afd51-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="afd51-244">Выберите **TodoContext (TodoApi.Models)** в поле **Класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="afd51-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="afd51-245">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="afd51-246">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="afd51-247">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="afd51-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="afd51-248">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="afd51-248">The preceding commands:</span></span>

* <span data-ttu-id="afd51-249">добавляют пакеты NuGet, необходимые для формирования шаблонов;</span><span class="sxs-lookup"><span data-stu-id="afd51-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="afd51-250">устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);</span><span class="sxs-lookup"><span data-stu-id="afd51-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="afd51-251">формируют шаблоны для `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="afd51-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="afd51-252">Сформированный код:</span><span class="sxs-lookup"><span data-stu-id="afd51-252">The generated code:</span></span>

* <span data-ttu-id="afd51-253">Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="afd51-253">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="afd51-254">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="afd51-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="afd51-255">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="afd51-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="afd51-256">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="afd51-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="afd51-257">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="afd51-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="afd51-258">Шаблоны ASP.NET Core для:</span><span class="sxs-lookup"><span data-stu-id="afd51-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="afd51-259">Контроллеры с представлениями включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="afd51-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="afd51-260">Контроллеры API не включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="afd51-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="afd51-261">Если токен `[action]` не находится в шаблоне маршрута, имя [действия](xref:mvc/controllers/routing#action) исключается из маршрута.</span><span class="sxs-lookup"><span data-stu-id="afd51-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="afd51-262">То есть имя связанного метода действия не используется в соответствующем маршруте.</span><span class="sxs-lookup"><span data-stu-id="afd51-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="afd51-263">Знакомство с методом создания PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="afd51-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="afd51-264">Измените инструкцию возврата в `PostTodoItem` и используйте оператор [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="afd51-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="afd51-265">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="afd51-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="afd51-266">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="afd51-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="afd51-267">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="afd51-267">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="afd51-268">Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="afd51-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="afd51-269">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="afd51-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="afd51-270">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="afd51-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="afd51-271">Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location).</span><span class="sxs-lookup"><span data-stu-id="afd51-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="afd51-272">Заголовок `Location` указывает [URI](https://developer.mozilla.org/docs/Glossary/URI) новой созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="afd51-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="afd51-273">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="afd51-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="afd51-274">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="afd51-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="afd51-275">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="afd51-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="afd51-276">Установка Postman</span><span class="sxs-lookup"><span data-stu-id="afd51-276">Install Postman</span></span>

<span data-ttu-id="afd51-277">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="afd51-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="afd51-278">Установка [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="afd51-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="afd51-279">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="afd51-279">Start the web app.</span></span>
* <span data-ttu-id="afd51-280">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="afd51-280">Start Postman.</span></span>
* <span data-ttu-id="afd51-281">Отключение параметра **Проверка SSL-сертификата**</span><span class="sxs-lookup"><span data-stu-id="afd51-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="afd51-282">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="afd51-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="afd51-283">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="afd51-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="afd51-284">Тестирование PostTodoItem с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="afd51-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="afd51-285">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="afd51-285">Create a new request.</span></span>
* <span data-ttu-id="afd51-286">Установите HTTP-метод `POST`.</span><span class="sxs-lookup"><span data-stu-id="afd51-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="afd51-287">Задайте для URI значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="afd51-287">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="afd51-288">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="afd51-288">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="afd51-289">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="afd51-289">Select the **Body** tab.</span></span>
* <span data-ttu-id="afd51-290">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="afd51-290">Select the **raw** radio button.</span></span>
* <span data-ttu-id="afd51-291">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="afd51-291">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="afd51-292">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="afd51-292">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="afd51-293">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-293">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="afd51-295">Тестирование URI заголовка расположения с помощью Postman</span><span class="sxs-lookup"><span data-stu-id="afd51-295">Test the location header URI with Postman</span></span>

* <span data-ttu-id="afd51-296">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="afd51-296">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="afd51-297">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="afd51-297">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="afd51-299">Установите HTTP-метод `GET`.</span><span class="sxs-lookup"><span data-stu-id="afd51-299">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="afd51-300">Задайте для URI значение `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="afd51-300">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="afd51-301">Например, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="afd51-301">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="afd51-302">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-302">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="afd51-303">Знакомство с методами GET</span><span class="sxs-lookup"><span data-stu-id="afd51-303">Examine the GET methods</span></span>

<span data-ttu-id="afd51-304">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="afd51-304">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="afd51-305">Протестируйте приложение, вызвав эти две конечные точки в браузере или в Postman.</span><span class="sxs-lookup"><span data-stu-id="afd51-305">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="afd51-306">Пример:</span><span class="sxs-lookup"><span data-stu-id="afd51-306">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="afd51-307">При вызове `GetTodoItems` возвращается примерно такой ответ:</span><span class="sxs-lookup"><span data-stu-id="afd51-307">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="afd51-308">Тестирование Get с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="afd51-308">Test Get with Postman</span></span>

* <span data-ttu-id="afd51-309">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="afd51-309">Create a new request.</span></span>
* <span data-ttu-id="afd51-310">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="afd51-310">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="afd51-311">Задайте для URI запроса значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="afd51-311">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="afd51-312">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="afd51-312">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="afd51-313">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="afd51-313">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="afd51-314">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-314">Select **Send**.</span></span>

<span data-ttu-id="afd51-315">Это приложение использует выполняющуюся в памяти базу данных.</span><span class="sxs-lookup"><span data-stu-id="afd51-315">This app uses an in-memory database.</span></span> <span data-ttu-id="afd51-316">Если остановить и вновь запустить его, предшествующий запрос GET не возвратит никаких данных.</span><span class="sxs-lookup"><span data-stu-id="afd51-316">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="afd51-317">Если данные не возвращаются, данные для приложения получаются методом [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="afd51-317">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="afd51-318">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="afd51-318">Routing and URL paths</span></span>

<span data-ttu-id="afd51-319">Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="afd51-319">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="afd51-320">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="afd51-320">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="afd51-321">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="afd51-321">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="afd51-322">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="afd51-322">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="afd51-323">В этом примере класс контроллера имеет имя **TodoItems**, а сам контроллер, соответственно, — "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="afd51-323">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="afd51-324">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="afd51-324">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="afd51-325">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="afd51-325">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="afd51-326">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="afd51-326">This sample doesn't use a template.</span></span> <span data-ttu-id="afd51-327">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="afd51-327">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="afd51-328">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="afd51-328">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="afd51-329">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="afd51-329">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="afd51-330">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="afd51-330">Return values</span></span>

<span data-ttu-id="afd51-331">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="afd51-331">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="afd51-332">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="afd51-332">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="afd51-333">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="afd51-333">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="afd51-334">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="afd51-334">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="afd51-335">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="afd51-335">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="afd51-336">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="afd51-336">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="afd51-337">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>).</span><span class="sxs-lookup"><span data-stu-id="afd51-337">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="afd51-338">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="afd51-338">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="afd51-339">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="afd51-339">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="afd51-340">Метод PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="afd51-340">The PutTodoItem method</span></span>

<span data-ttu-id="afd51-341">Проверьте метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="afd51-341">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="afd51-342">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="afd51-342">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="afd51-343">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="afd51-343">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="afd51-344">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="afd51-344">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="afd51-345">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="afd51-345">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="afd51-346">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="afd51-346">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="afd51-347">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="afd51-347">Test the PutTodoItem method</span></span>

<span data-ttu-id="afd51-348">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="afd51-348">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="afd51-349">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="afd51-349">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="afd51-350">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="afd51-350">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="afd51-351">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="afd51-351">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="afd51-352">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="afd51-352">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="afd51-354">Метод DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="afd51-354">The DeleteTodoItem method</span></span>

<span data-ttu-id="afd51-355">Проверьте метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="afd51-355">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="afd51-356">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="afd51-356">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="afd51-357">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="afd51-357">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="afd51-358">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="afd51-358">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="afd51-359">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="afd51-359">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="afd51-360">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-360">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="afd51-361">Предотвращение избыточной публикации</span><span class="sxs-lookup"><span data-stu-id="afd51-361">Prevent over-posting</span></span>

<span data-ttu-id="afd51-362">В настоящее время пример приложения предоставляет весь объект `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="afd51-362">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="afd51-363">Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели.</span><span class="sxs-lookup"><span data-stu-id="afd51-363">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="afd51-364">Это связано с несколькими причинами, и безопасность является основной.</span><span class="sxs-lookup"><span data-stu-id="afd51-364">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="afd51-365">Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления.</span><span class="sxs-lookup"><span data-stu-id="afd51-365">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="afd51-366">В этой статье используется **DTO**.</span><span class="sxs-lookup"><span data-stu-id="afd51-366">**DTO** is used in this article.</span></span>

<span data-ttu-id="afd51-367">DTO можно использовать для следующего:</span><span class="sxs-lookup"><span data-stu-id="afd51-367">A DTO may be used to:</span></span>

* <span data-ttu-id="afd51-368">Предотвращение избыточной публикации.</span><span class="sxs-lookup"><span data-stu-id="afd51-368">Prevent over-posting.</span></span>
* <span data-ttu-id="afd51-369">Скрытие свойств, которые не предназначены для просмотра клиентами.</span><span class="sxs-lookup"><span data-stu-id="afd51-369">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="afd51-370">Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="afd51-370">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="afd51-371">Сведение графов объектов, содержащих вложенные объекты.</span><span class="sxs-lookup"><span data-stu-id="afd51-371">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="afd51-372">Сведенные графы объектов могут быть удобнее для клиентов.</span><span class="sxs-lookup"><span data-stu-id="afd51-372">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="afd51-373">Чтобы продемонстрировать подход с применением DTO, обновите класс `TodoItem`, включив в него поле секрета:</span><span class="sxs-lookup"><span data-stu-id="afd51-373">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="afd51-374">Поле секрета должно быть скрыто в этом приложении, однако административное приложение может отобразить его.</span><span class="sxs-lookup"><span data-stu-id="afd51-374">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="afd51-375">Убедитесь, что вы можете отправить и получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="afd51-375">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="afd51-376">Создайте модель DTO:</span><span class="sxs-lookup"><span data-stu-id="afd51-376">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="afd51-377">Обновите `TodoItemsController` для использования `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="afd51-377">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="afd51-378">Убедитесь, что вы можете отправить или получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="afd51-378">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="afd51-379">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="afd51-379">Call the web API with JavaScript</span></span>

<span data-ttu-id="afd51-380">См. руководство по [: Вызовите веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="afd51-380">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="afd51-381">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="afd51-381">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="afd51-382">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="afd51-382">Create a web API project.</span></span>
> * <span data-ttu-id="afd51-383">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="afd51-383">Add a model class and a database context.</span></span>
> * <span data-ttu-id="afd51-384">Добавление контроллера.</span><span class="sxs-lookup"><span data-stu-id="afd51-384">Add a controller.</span></span>
> * <span data-ttu-id="afd51-385">Добавление методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="afd51-385">Add CRUD methods.</span></span>
> * <span data-ttu-id="afd51-386">Настройка маршрутизации и путей URL.</span><span class="sxs-lookup"><span data-stu-id="afd51-386">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="afd51-387">Указание возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="afd51-387">Specify return values.</span></span>
> * <span data-ttu-id="afd51-388">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="afd51-388">Call the web API with Postman.</span></span>
> * <span data-ttu-id="afd51-389">Вызовите веб-API с помощью JavaScript.</span><span class="sxs-lookup"><span data-stu-id="afd51-389">Call the web API with JavaScript.</span></span>

<span data-ttu-id="afd51-390">В конечном итоге вы получите веб-API, обеспечивающий управление элементами списка дел, хранящимися в реляционной базе данных.</span><span class="sxs-lookup"><span data-stu-id="afd51-390">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="afd51-391">Обзор</span><span class="sxs-lookup"><span data-stu-id="afd51-391">Overview</span></span>

<span data-ttu-id="afd51-392">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="afd51-392">This tutorial creates the following API:</span></span>

|<span data-ttu-id="afd51-393">API</span><span class="sxs-lookup"><span data-stu-id="afd51-393">API</span></span> | <span data-ttu-id="afd51-394">Описание</span><span class="sxs-lookup"><span data-stu-id="afd51-394">Description</span></span> | <span data-ttu-id="afd51-395">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="afd51-395">Request body</span></span> | <span data-ttu-id="afd51-396">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="afd51-396">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="afd51-397">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="afd51-397">GET /api/TodoItems</span></span> | <span data-ttu-id="afd51-398">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="afd51-398">Get all to-do items</span></span> | <span data-ttu-id="afd51-399">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="afd51-399">None</span></span> | <span data-ttu-id="afd51-400">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="afd51-400">Array of to-do items</span></span>|
|<span data-ttu-id="afd51-401">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="afd51-401">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="afd51-402">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="afd51-402">Get an item by ID</span></span> | <span data-ttu-id="afd51-403">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="afd51-403">None</span></span> | <span data-ttu-id="afd51-404">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="afd51-404">To-do item</span></span>|
|<span data-ttu-id="afd51-405">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="afd51-405">POST /api/TodoItems</span></span> | <span data-ttu-id="afd51-406">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="afd51-406">Add a new item</span></span> | <span data-ttu-id="afd51-407">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="afd51-407">To-do item</span></span> | <span data-ttu-id="afd51-408">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="afd51-408">To-do item</span></span> |
|<span data-ttu-id="afd51-409">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="afd51-409">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="afd51-410">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="afd51-410">Update an existing item &nbsp;</span></span> | <span data-ttu-id="afd51-411">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="afd51-411">To-do item</span></span> | <span data-ttu-id="afd51-412">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="afd51-412">None</span></span> |
|<span data-ttu-id="afd51-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="afd51-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="afd51-414">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="afd51-414">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="afd51-415">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="afd51-415">None</span></span> | <span data-ttu-id="afd51-416">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="afd51-416">None</span></span>|

<span data-ttu-id="afd51-417">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="afd51-417">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="afd51-423">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="afd51-423">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-424">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="afd51-425">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="afd51-425">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="afd51-426">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-426">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="afd51-427">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="afd51-427">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-428">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="afd51-429">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="afd51-429">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="afd51-430">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afd51-430">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="afd51-431">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="afd51-431">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="afd51-432">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="afd51-432">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="afd51-433">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="afd51-433">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="afd51-434">**Не** выбирайте **Включение поддержки Docker**.</span><span class="sxs-lookup"><span data-stu-id="afd51-434">**Don't** select **Enable Docker Support**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="afd51-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="afd51-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="afd51-437">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="afd51-437">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="afd51-438">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="afd51-438">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="afd51-439">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="afd51-439">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="afd51-440">С помощью этих команд создается новый проект веб-API и открывается новый экземпляр Visual Studio Code в новой папке проекта.</span><span class="sxs-lookup"><span data-stu-id="afd51-440">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="afd51-441">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="afd51-441">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="afd51-442">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="afd51-443">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="afd51-443">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="afd51-445">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afd51-445">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="afd51-446">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afd51-446">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="afd51-447">В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **целевой платформы** выберите последнюю версию .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="afd51-447">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="afd51-448">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afd51-448">Select **Next**.</span></span>

* <span data-ttu-id="afd51-449">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="afd51-449">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="afd51-451">Тестирование API</span><span class="sxs-lookup"><span data-stu-id="afd51-451">Test the API</span></span>

<span data-ttu-id="afd51-452">Шаблон проекта создает API `values`.</span><span class="sxs-lookup"><span data-stu-id="afd51-452">The project template creates a `values` API.</span></span> <span data-ttu-id="afd51-453">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="afd51-453">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-454">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-454">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="afd51-455">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="afd51-455">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="afd51-456">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/api/values`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="afd51-456">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="afd51-457">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="afd51-457">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="afd51-458">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="afd51-458">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="afd51-459">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="afd51-459">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="afd51-460">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="afd51-460">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="afd51-461">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="afd51-461">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="afd51-462">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-462">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="afd51-463">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="afd51-463">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="afd51-464">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="afd51-464">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="afd51-465">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="afd51-465">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="afd51-466">Добавьте `/api/values` к URL-адресу (измените URL-адрес на `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="afd51-466">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="afd51-467">Возвращается следующий файл JSON:</span><span class="sxs-lookup"><span data-stu-id="afd51-467">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="afd51-468">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="afd51-468">Add a model class</span></span>

<span data-ttu-id="afd51-469">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="afd51-469">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="afd51-470">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="afd51-470">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="afd51-472">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="afd51-472">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="afd51-473">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="afd51-473">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="afd51-474">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="afd51-474">Name the folder *Models*.</span></span>

* <span data-ttu-id="afd51-475">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="afd51-475">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="afd51-476">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-476">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="afd51-477">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="afd51-477">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="afd51-478">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="afd51-478">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="afd51-479">Добавьте папку с именем *Models* .</span><span class="sxs-lookup"><span data-stu-id="afd51-479">Add a folder named *Models*.</span></span>

* <span data-ttu-id="afd51-480">Добавьте в папку *Models* класс `TodoItem` со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="afd51-480">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="afd51-481">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-481">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="afd51-482">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="afd51-482">Right-click the project.</span></span> <span data-ttu-id="afd51-483">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="afd51-483">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="afd51-484">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="afd51-484">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="afd51-486">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="afd51-486">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="afd51-487">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="afd51-487">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="afd51-488">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="afd51-488">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="afd51-489">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="afd51-489">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="afd51-490">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .</span><span class="sxs-lookup"><span data-stu-id="afd51-490">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="afd51-491">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="afd51-491">Add a database context</span></span>

<span data-ttu-id="afd51-492">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="afd51-492">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="afd51-493">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="afd51-493">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-494">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-494">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="afd51-495">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="afd51-495">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="afd51-496">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-496">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="afd51-497">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-497">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="afd51-498">Добавьте класс `TodoContext` в папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="afd51-498">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="afd51-499">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="afd51-499">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="afd51-500">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="afd51-500">Register the database context</span></span>

<span data-ttu-id="afd51-501">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="afd51-501">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="afd51-502">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="afd51-502">The container provides the service to controllers.</span></span>

<span data-ttu-id="afd51-503">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="afd51-503">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="afd51-504">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="afd51-504">The preceding code:</span></span>

* <span data-ttu-id="afd51-505">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="afd51-505">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="afd51-506">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="afd51-506">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="afd51-507">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="afd51-507">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="afd51-508">Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="afd51-508">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-509">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="afd51-510">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="afd51-510">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="afd51-511">Щелкните **Добавить** > **Создать элемент**.</span><span class="sxs-lookup"><span data-stu-id="afd51-511">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="afd51-512">В диалоговом окне **Добавить новый элемент** выберите шаблон **Класс контроллера API**.</span><span class="sxs-lookup"><span data-stu-id="afd51-512">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="afd51-513">Присвойте классу имя *TodoController* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-513">Name the class *TodoController*, and select **Add**.</span></span>

  ![Диалоговое окно добавления элемента с контроллером в поле поиска и выбранным контроллером веб-API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="afd51-515">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-515">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="afd51-516">В папке *Controllers* создайте класс с именем `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="afd51-516">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="afd51-517">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="afd51-517">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="afd51-518">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="afd51-518">The preceding code:</span></span>

* <span data-ttu-id="afd51-519">Определяет класс контроллера API без методов.</span><span class="sxs-lookup"><span data-stu-id="afd51-519">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="afd51-520">Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="afd51-520">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="afd51-521">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="afd51-521">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="afd51-522">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="afd51-522">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="afd51-523">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="afd51-523">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="afd51-524">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="afd51-524">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="afd51-525">Добавляет элемент `Item1` в базу данных, если она пуста.</span><span class="sxs-lookup"><span data-stu-id="afd51-525">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="afd51-526">Этот код находится в конструкторе и выполняется каждый раз при обнаружении нового HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="afd51-526">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="afd51-527">Если вы удалите все элементы, конструктор создаст `Item1` при следующем вызове метода API.</span><span class="sxs-lookup"><span data-stu-id="afd51-527">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="afd51-528">Поэтому может создаться впечатление, что удаление не было выполнено, хотя это не так.</span><span class="sxs-lookup"><span data-stu-id="afd51-528">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="afd51-529">Добавление методов Get</span><span class="sxs-lookup"><span data-stu-id="afd51-529">Add Get methods</span></span>

<span data-ttu-id="afd51-530">Чтобы получить API, который извлекает элементы списка дел, добавьте следующие методы в класс `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="afd51-530">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="afd51-531">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="afd51-531">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="afd51-532">Если приложение все еще выполняется, оно останавливается.</span><span class="sxs-lookup"><span data-stu-id="afd51-532">Stop the app if it's still running.</span></span> <span data-ttu-id="afd51-533">Затем оно запускается снова с последними изменениями.</span><span class="sxs-lookup"><span data-stu-id="afd51-533">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="afd51-534">Протестируйте приложение, вызвав эти две конечные точки в браузере.</span><span class="sxs-lookup"><span data-stu-id="afd51-534">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="afd51-535">Пример:</span><span class="sxs-lookup"><span data-stu-id="afd51-535">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="afd51-536">При вызове `GetTodoItems` возвращается следующий ответ HTTP:</span><span class="sxs-lookup"><span data-stu-id="afd51-536">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="afd51-537">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="afd51-537">Routing and URL paths</span></span>

<span data-ttu-id="afd51-538">Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="afd51-538">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="afd51-539">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="afd51-539">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="afd51-540">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="afd51-540">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="afd51-541">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="afd51-541">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="afd51-542">В этом примере класс контроллера носит имя **Todo**, а сам контроллер, соответственно, — "todo".</span><span class="sxs-lookup"><span data-stu-id="afd51-542">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="afd51-543">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="afd51-543">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="afd51-544">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="afd51-544">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="afd51-545">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="afd51-545">This sample doesn't use a template.</span></span> <span data-ttu-id="afd51-546">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="afd51-546">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="afd51-547">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="afd51-547">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="afd51-548">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="afd51-548">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="afd51-549">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="afd51-549">Return values</span></span>

<span data-ttu-id="afd51-550">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="afd51-550">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="afd51-551">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="afd51-551">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="afd51-552">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="afd51-552">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="afd51-553">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="afd51-553">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="afd51-554">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="afd51-554">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="afd51-555">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="afd51-555">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="afd51-556">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>).</span><span class="sxs-lookup"><span data-stu-id="afd51-556">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="afd51-557">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="afd51-557">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="afd51-558">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="afd51-558">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="afd51-559">Тестирование метода GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="afd51-559">Test the GetTodoItems method</span></span>

<span data-ttu-id="afd51-560">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="afd51-560">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="afd51-561">Установите [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="afd51-561">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="afd51-562">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="afd51-562">Start the web app.</span></span>
* <span data-ttu-id="afd51-563">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="afd51-563">Start Postman.</span></span>
* <span data-ttu-id="afd51-564">Отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="afd51-564">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="afd51-565">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afd51-565">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="afd51-566">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="afd51-566">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="afd51-567">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="afd51-567">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="afd51-568">В меню **Postman** > **Настройки** (вкладка **Общие**) отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="afd51-568">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="afd51-569">Кроме того, можно выбрать значок гаечного ключа и выбрать **Параметры**, а затем отключить проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="afd51-569">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="afd51-570">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="afd51-570">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="afd51-571">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="afd51-571">Create a new request.</span></span>
  * <span data-ttu-id="afd51-572">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="afd51-572">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="afd51-573">Задайте для URI запроса значение `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="afd51-573">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="afd51-574">Например, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="afd51-574">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="afd51-575">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="afd51-575">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="afd51-576">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-576">Select **Send**.</span></span>

![Postman с запросом Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="afd51-578">Добавление метода Create</span><span class="sxs-lookup"><span data-stu-id="afd51-578">Add a Create method</span></span>

<span data-ttu-id="afd51-579">Добавьте следующий метод `PostTodoItem` в *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="afd51-579">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="afd51-580">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="afd51-580">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="afd51-581">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="afd51-581">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="afd51-582">Метод `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="afd51-582">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="afd51-583">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="afd51-583">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="afd51-584">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="afd51-584">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="afd51-585">Добавляет заголовок `Location` в ответ.</span><span class="sxs-lookup"><span data-stu-id="afd51-585">Adds a `Location` header to the response.</span></span> <span data-ttu-id="afd51-586">Заголовок `Location` расположения указывает URI вновь созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="afd51-586">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="afd51-587">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="afd51-587">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="afd51-588">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="afd51-588">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="afd51-589">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="afd51-589">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="afd51-590">Тестирование метода PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="afd51-590">Test the PostTodoItem method</span></span>

* <span data-ttu-id="afd51-591">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="afd51-591">Build the project.</span></span>
* <span data-ttu-id="afd51-592">В Postman укажите метод HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="afd51-592">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="afd51-593">Задайте для URI значение `https://localhost:<port>/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="afd51-593">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="afd51-594">Например, `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="afd51-594">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="afd51-595">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="afd51-595">Select the **Body** tab.</span></span>
* <span data-ttu-id="afd51-596">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="afd51-596">Select the **raw** radio button.</span></span>
* <span data-ttu-id="afd51-597">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="afd51-597">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="afd51-598">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="afd51-598">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="afd51-599">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-599">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/create.png)

  <span data-ttu-id="afd51-601">Если вы получаете ошибку 405 "Недопустимый метод", это может свидетельствовать о том, что после добавления метода `PostTodoItem` не была выполнена компиляция проекта.</span><span class="sxs-lookup"><span data-stu-id="afd51-601">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="afd51-602">Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="afd51-602">Test the location header URI</span></span>

* <span data-ttu-id="afd51-603">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="afd51-603">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="afd51-604">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="afd51-604">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="afd51-606">Укажите метод GET.</span><span class="sxs-lookup"><span data-stu-id="afd51-606">Set the method to GET.</span></span>
<span data-ttu-id="afd51-607">\* Задайте для URI значение  `https://localhost:<port>/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="afd51-607">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="afd51-608"> Например:  `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="afd51-608"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="afd51-609">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-609">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="afd51-610">Добавление метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="afd51-610">Add a PutTodoItem method</span></span>

<span data-ttu-id="afd51-611">Добавьте приведенный ниже метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="afd51-611">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="afd51-612">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="afd51-612">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="afd51-613">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="afd51-613">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="afd51-614">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="afd51-614">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="afd51-615">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="afd51-615">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="afd51-616">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="afd51-616">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="afd51-617">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="afd51-617">Test the PutTodoItem method</span></span>

<span data-ttu-id="afd51-618">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="afd51-618">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="afd51-619">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="afd51-619">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="afd51-620">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="afd51-620">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="afd51-621">Обновите элемент списка дел с идентификатором = 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="afd51-621">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="afd51-622">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="afd51-622">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="afd51-624">Добавление метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="afd51-624">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="afd51-625">Добавьте приведенный ниже метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="afd51-625">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="afd51-626">`DeleteTodoItem`Ответ[ — 204 (нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="afd51-626">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="afd51-627">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="afd51-627">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="afd51-628">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="afd51-628">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="afd51-629">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="afd51-629">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="afd51-630">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="afd51-630">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="afd51-631">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="afd51-631">Select **Send**.</span></span>

<span data-ttu-id="afd51-632">В этом примере приложения вы можете удалить все элементы.</span><span class="sxs-lookup"><span data-stu-id="afd51-632">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="afd51-633">Однако в случае удаления последнего элемента в момент следующего вызова API конструктор класса модели создаст новый элемент.</span><span class="sxs-lookup"><span data-stu-id="afd51-633">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="afd51-634">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="afd51-634">Call the web API with JavaScript</span></span>

<span data-ttu-id="afd51-635">В этом разделе описано, как добавить HTML-страницу, которая использует JavaScript для вызова веб-API.</span><span class="sxs-lookup"><span data-stu-id="afd51-635">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="afd51-636">jQuery инициирует запрос.</span><span class="sxs-lookup"><span data-stu-id="afd51-636">jQuery initiates the request.</span></span> <span data-ttu-id="afd51-637">JavaScript изменяет страницу, используя сведения из ответа API.</span><span class="sxs-lookup"><span data-stu-id="afd51-637">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="afd51-638">Настройте приложение для [обслуживания статических файлов](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) и [включения сопоставления файлов по умолчанию](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A), обновив *Startup.cs* следующим выделенным кодом:</span><span class="sxs-lookup"><span data-stu-id="afd51-638">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="afd51-639">Создайте папку *wwwroot* в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="afd51-639">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="afd51-640">Добавьте HTML-файл *index.html* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="afd51-640">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="afd51-641">Замените его содержимое следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="afd51-641">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="afd51-642">Добавьте файл JavaScript с именем *site.js* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="afd51-642">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="afd51-643">Замените его содержимое следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="afd51-643">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="afd51-644">Может потребоваться изменение параметров запуска проекта ASP.NET Core для локального тестирования HTML-страницы:</span><span class="sxs-lookup"><span data-stu-id="afd51-644">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="afd51-645">Откройте файл *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="afd51-645">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="afd51-646">Удалите свойство `launchUrl`, чтобы приложение открылось через *index.html* &mdash; файл проекта по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="afd51-646">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="afd51-647">В этом примере вызываются все методы CRUD в веб-API.</span><span class="sxs-lookup"><span data-stu-id="afd51-647">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="afd51-648">Ниже приводится пояснение вызовов API.</span><span class="sxs-lookup"><span data-stu-id="afd51-648">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="afd51-649">Получение списка элементов задач</span><span class="sxs-lookup"><span data-stu-id="afd51-649">Get a list of to-do items</span></span>

<span data-ttu-id="afd51-650">jQuery отправляет запрос HTTP GET к веб-API, который возвращает ответ JSON, представляющий массив элементов списка дел.</span><span class="sxs-lookup"><span data-stu-id="afd51-650">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="afd51-651">В случае успешного запроса используется функция обратного вызова `success`.</span><span class="sxs-lookup"><span data-stu-id="afd51-651">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="afd51-652">При обратном вызове в модель DOM вносятся данные о задачах.</span><span class="sxs-lookup"><span data-stu-id="afd51-652">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="afd51-653">Добавление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="afd51-653">Add a to-do item</span></span>

<span data-ttu-id="afd51-654">jQuery отправляет запрос HTTP POST с элементом списка дел в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="afd51-654">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="afd51-655">Для параметров `accepts` и `contentType` указывается `application/json`, чтобы указать тип носителя при получении и отправке.</span><span class="sxs-lookup"><span data-stu-id="afd51-655">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="afd51-656">Элемент списка дел преобразуется в JSON с помощью [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="afd51-656">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="afd51-657">Если интерфейс API возвращает код состояния успешного выполнения, вызывается функция `getData` для обновления HTML-таблицы.</span><span class="sxs-lookup"><span data-stu-id="afd51-657">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="afd51-658">Обновление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="afd51-658">Update a to-do item</span></span>

<span data-ttu-id="afd51-659">Обновление элемента списка дел выполняется аналогично его добавлению.</span><span class="sxs-lookup"><span data-stu-id="afd51-659">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="afd51-660">`url` изменяется, чтобы добавить уникальный идентификатор для элемента, а в качестве `type` устанавливается `PUT`.</span><span class="sxs-lookup"><span data-stu-id="afd51-660">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="afd51-661">Удаление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="afd51-661">Delete a to-do item</span></span>

<span data-ttu-id="afd51-662">Чтобы удалить элемент списка дел, установите для `type` в вызове AJAX значение `DELETE` и укажите уникальный идентификатор элемента в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="afd51-662">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="afd51-663">Добавление поддержки аутентификации в веб-API</span><span class="sxs-lookup"><span data-stu-id="afd51-663">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="afd51-664">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="afd51-664">Additional resources</span></span>

<span data-ttu-id="afd51-665">[Просмотреть или скачать пример кода для этого учебника](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="afd51-665">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="afd51-666">См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="afd51-666">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="afd51-667">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="afd51-667">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="afd51-668">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="afd51-668">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
