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
ms.openlocfilehash: 7f684d96ee9fd640abdc750503bed1b2a836a459
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847754"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="9643d-103">Учебник. Создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9643d-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="9643d-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Майк Уоссон](https://github.com/mikewasson) (Mike Wasson)</span><span class="sxs-lookup"><span data-stu-id="9643d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="9643d-105">В этом учебнике приводятся основные сведения о создании веб-API с помощью ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9643d-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9643d-106">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="9643d-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9643d-107">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="9643d-107">Create a web API project.</span></span>
> * <span data-ttu-id="9643d-108">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="9643d-109">Формирование шаблонов контроллера с использованием методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="9643d-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="9643d-110">Настройка маршрутизации, URL-пути и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="9643d-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="9643d-111">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-111">Call the web API with Postman.</span></span>

<span data-ttu-id="9643d-112">В итоге вы получите веб-API, позволяющий работать с элементами списка дел, хранимыми в базе данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="9643d-113">Обзор</span><span class="sxs-lookup"><span data-stu-id="9643d-113">Overview</span></span>

<span data-ttu-id="9643d-114">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="9643d-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="9643d-115">API</span><span class="sxs-lookup"><span data-stu-id="9643d-115">API</span></span> | <span data-ttu-id="9643d-116">Описание</span><span class="sxs-lookup"><span data-stu-id="9643d-116">Description</span></span> | <span data-ttu-id="9643d-117">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="9643d-117">Request body</span></span> | <span data-ttu-id="9643d-118">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="9643d-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="9643d-119">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="9643d-119">Get all to-do items</span></span> | <span data-ttu-id="9643d-120">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-120">None</span></span> | <span data-ttu-id="9643d-121">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="9643d-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="9643d-122">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="9643d-122">Get an item by ID</span></span> | <span data-ttu-id="9643d-123">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-123">None</span></span> | <span data-ttu-id="9643d-124">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="9643d-125">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="9643d-125">Add a new item</span></span> | <span data-ttu-id="9643d-126">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-126">To-do item</span></span> | <span data-ttu-id="9643d-127">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="9643d-128">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9643d-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="9643d-129">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-129">To-do item</span></span> | <span data-ttu-id="9643d-130">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-130">None</span></span> |
|<span data-ttu-id="9643d-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9643d-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="9643d-132">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9643d-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="9643d-133">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-133">None</span></span> | <span data-ttu-id="9643d-134">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-134">None</span></span>|

<span data-ttu-id="9643d-135">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="9643d-135">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="9643d-141">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="9643d-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9643d-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9643d-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9643d-144">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="9643d-145">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="9643d-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9643d-147">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="9643d-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9643d-148">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="9643d-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="9643d-149">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="9643d-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="9643d-150">Убедитесь, что в диалоговом окне **Создание веб-приложения ASP.NET Core** выбраны платформы **.NET Core** и **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="9643d-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="9643d-151">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="9643d-151">Select the **API** template and click **Create**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9643d-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9643d-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9643d-154">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9643d-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9643d-155">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="9643d-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="9643d-156">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="9643d-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="9643d-157">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="9643d-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="9643d-158">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="9643d-158">The preceding commands:</span></span>

  * <span data-ttu-id="9643d-159">Создает новый проект веб-API и открывает его в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9643d-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="9643d-160">Добавляет пакеты NuGet, которые понадобятся в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="9643d-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9643d-161">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9643d-162">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="9643d-162">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="9643d-164">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="9643d-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="9643d-165">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="9643d-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Выбор шаблона API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="9643d-167">В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **целевой платформы** выберите последнюю версию .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="9643d-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="9643d-168">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="9643d-168">Select **Next**.</span></span>

* <span data-ttu-id="9643d-169">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="9643d-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="9643d-171">Откройте командный терминал в папке проекта и выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="9643d-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="9643d-172">Тестирование проекта</span><span class="sxs-lookup"><span data-stu-id="9643d-172">Test the project</span></span>

<span data-ttu-id="9643d-173">Шаблон проекта создает API `WeatherForecast` с поддержкой [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="9643d-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9643d-175">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="9643d-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="9643d-176">Visual Studio запустит:</span><span class="sxs-lookup"><span data-stu-id="9643d-176">Visual Studio launches:</span></span>

* <span data-ttu-id="9643d-177">веб-сервер IIS Express;</span><span class="sxs-lookup"><span data-stu-id="9643d-177">The IIS Express web server.</span></span>
* <span data-ttu-id="9643d-178">браузер по умолчанию и перейдет к `https://localhost:<port>/https://localhost:5001/swagger/index.html`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="9643d-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9643d-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9643d-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="9643d-180">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9643d-181">В браузере перейдите по следующему URL-адресу: [https://localhost:5001/swagger](https://localhost:5001/swagger).</span><span class="sxs-lookup"><span data-stu-id="9643d-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9643d-182">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9643d-183">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="9643d-184">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="9643d-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="9643d-185">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="9643d-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="9643d-186">Добавьте `/swagger` к URL-адресу (измените URL-адрес на `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="9643d-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="9643d-187">Откроется страница Swagger `/swagger/index.html`.</span><span class="sxs-lookup"><span data-stu-id="9643d-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="9643d-188">Выберите **Get** (Получить) > **Try it out** (Попробовать) > **Execute** (Выполнить).</span><span class="sxs-lookup"><span data-stu-id="9643d-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="9643d-189">На странице отобразятся:</span><span class="sxs-lookup"><span data-stu-id="9643d-189">The page displays:</span></span>

* <span data-ttu-id="9643d-190">команда [Curl](https://curl.haxx.se/) для тестирования API WeatherForecast;</span><span class="sxs-lookup"><span data-stu-id="9643d-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="9643d-191">URL-адрес для тестирования API WeatherForecast;</span><span class="sxs-lookup"><span data-stu-id="9643d-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="9643d-192">код, текст и заголовки ответа;</span><span class="sxs-lookup"><span data-stu-id="9643d-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="9643d-193">раскрывающийся список с типами мультимедиа и примером значения и схемы.</span><span class="sxs-lookup"><span data-stu-id="9643d-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="9643d-194">Swagger используется для создания полезной документации и страниц справки для веб-API.</span><span class="sxs-lookup"><span data-stu-id="9643d-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="9643d-195">В этом учебнике рассматривается создание веб-API.</span><span class="sxs-lookup"><span data-stu-id="9643d-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="9643d-196">Дополнительные сведения о Swagger: <xref:tutorials/web-api-help-pages-using-swagger>.</span><span class="sxs-lookup"><span data-stu-id="9643d-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="9643d-197">Скопируйте и вставьте **URL-адрес запроса** в строку браузера: `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="9643d-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="9643d-198">Возвращаемые данные JSON будут выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="9643d-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="9643d-199">Обновление launchUrl</span><span class="sxs-lookup"><span data-stu-id="9643d-199">Update the launchUrl</span></span>

<span data-ttu-id="9643d-200">В файле *Properties/launchSettings.json* обновите `launchUrl` с `"swagger"` на `"api/TodoItems"`.</span><span class="sxs-lookup"><span data-stu-id="9643d-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="9643d-201">Поскольку Swagger удален, предыдущая разметка изменит URL-адрес, который запускается в методе GET контроллера, добавленного в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="9643d-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="9643d-202">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="9643d-202">Add a model class</span></span>

<span data-ttu-id="9643d-203">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="9643d-204">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9643d-206">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="9643d-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="9643d-207">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="9643d-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9643d-208">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="9643d-209">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="9643d-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9643d-210">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="9643d-211">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9643d-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9643d-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9643d-213">Добавьте папку с именем *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="9643d-214">Добавьте в папку *Models* класс `TodoItem` со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9643d-215">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9643d-216">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="9643d-216">Right-click the project.</span></span> <span data-ttu-id="9643d-217">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="9643d-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9643d-218">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-218">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="9643d-220">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="9643d-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="9643d-221">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="9643d-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="9643d-222">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="9643d-223">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="9643d-224">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="9643d-225">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="9643d-225">Add a database context</span></span>

<span data-ttu-id="9643d-226">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="9643d-227">Этот класс является производным от класса <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="9643d-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="9643d-229">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="9643d-229">Add NuGet packages</span></span>

* <span data-ttu-id="9643d-230">В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.</span><span class="sxs-lookup"><span data-stu-id="9643d-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="9643d-231">Откройте вкладку **Обзор** и в поле поиска введите \*\*Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9643d-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="9643d-232">**EntityFrameworkCore.SqlServer**.</span><span class="sxs-lookup"><span data-stu-id="9643d-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="9643d-233">Установите флажок **Включить предварительные выпуски** , чтобы был доступен релиз-кандидат версии 5.0.</span><span class="sxs-lookup"><span data-stu-id="9643d-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="9643d-234">На панели слева выберите **Microsoft.EntityFrameworkCore.SqlServer**.</span><span class="sxs-lookup"><span data-stu-id="9643d-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="9643d-235">Установите флажок **Проект** на правой панели и выберите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-235">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="9643d-236">Добавьте пакет NuGet **пакет NuGet Microsoft.EntityFrameworkCore.InMemory**, выполнив приведенные выше инструкции.</span><span class="sxs-lookup"><span data-stu-id="9643d-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="9643d-237">![диспетчер пакетов NuGet](first-web-api/_static/5/vsNuGet.png);</span><span class="sxs-lookup"><span data-stu-id="9643d-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="9643d-238">Добавление контекста базы данных TodoContext</span><span class="sxs-lookup"><span data-stu-id="9643d-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="9643d-239">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="9643d-239">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9643d-240">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-240">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9643d-241">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9643d-242">Добавьте класс `TodoContext` в папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-242">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="9643d-243">Введите следующий код:</span><span class="sxs-lookup"><span data-stu-id="9643d-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="9643d-244">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="9643d-244">Register the database context</span></span>

<span data-ttu-id="9643d-245">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9643d-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9643d-246">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="9643d-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="9643d-247">Обновите файл *Startup.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="9643d-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="9643d-248">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="9643d-248">The preceding code:</span></span>

* <span data-ttu-id="9643d-249">Удаляет вызовы Swagger.</span><span class="sxs-lookup"><span data-stu-id="9643d-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="9643d-250">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="9643d-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="9643d-251">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="9643d-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="9643d-252">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="9643d-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="9643d-253">Формирование шаблонов контроллера</span><span class="sxs-lookup"><span data-stu-id="9643d-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9643d-255">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="9643d-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="9643d-256">Щелкните **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="9643d-256">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="9643d-257">Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-257">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="9643d-258">В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="9643d-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="9643d-259">Выберите **TodoItem (TodoApi.Models)** в поле **Класс модели**.</span><span class="sxs-lookup"><span data-stu-id="9643d-259">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="9643d-260">Выберите **TodoContext (TodoApi.Models)** в поле **Класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="9643d-260">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="9643d-261">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-261">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9643d-262">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9643d-263">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="9643d-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="9643d-264">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="9643d-264">The preceding commands:</span></span>

* <span data-ttu-id="9643d-265">добавляют пакеты NuGet, необходимые для формирования шаблонов;</span><span class="sxs-lookup"><span data-stu-id="9643d-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="9643d-266">устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);</span><span class="sxs-lookup"><span data-stu-id="9643d-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="9643d-267">формируют шаблоны для `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="9643d-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="9643d-268">Сформированный код:</span><span class="sxs-lookup"><span data-stu-id="9643d-268">The generated code:</span></span>

* <span data-ttu-id="9643d-269">Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="9643d-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="9643d-270">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="9643d-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="9643d-271">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="9643d-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="9643d-272">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="9643d-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="9643d-273">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="9643d-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="9643d-274">Шаблоны ASP.NET Core для:</span><span class="sxs-lookup"><span data-stu-id="9643d-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="9643d-275">Контроллеры с представлениями включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="9643d-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="9643d-276">Контроллеры API не включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="9643d-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="9643d-277">Если токен `[action]` не находится в шаблоне маршрута, имя [действия](xref:mvc/controllers/routing#action) исключается из маршрута.</span><span class="sxs-lookup"><span data-stu-id="9643d-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="9643d-278">То есть имя связанного метода действия не используется в соответствующем маршруте.</span><span class="sxs-lookup"><span data-stu-id="9643d-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="9643d-279">Обновление метода создания PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="9643d-280">Измените инструкцию возврата в `PostTodoItem` и используйте оператор [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="9643d-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="9643d-281">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="9643d-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="9643d-282">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="9643d-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="9643d-283">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="9643d-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9643d-284">Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="9643d-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="9643d-285">В случае успеха возвращает [код состояния HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201).</span><span class="sxs-lookup"><span data-stu-id="9643d-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="9643d-286">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="9643d-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="9643d-287">Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location).</span><span class="sxs-lookup"><span data-stu-id="9643d-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="9643d-288">Заголовок `Location` указывает [URI](https://developer.mozilla.org/docs/Glossary/URI) новой созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="9643d-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="9643d-289">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="9643d-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="9643d-290">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="9643d-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="9643d-291">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="9643d-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="9643d-292">Установка Postman</span><span class="sxs-lookup"><span data-stu-id="9643d-292">Install Postman</span></span>

<span data-ttu-id="9643d-293">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="9643d-294">Установка [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="9643d-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="9643d-295">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-295">Start the web app.</span></span>
* <span data-ttu-id="9643d-296">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-296">Start Postman.</span></span>
* <span data-ttu-id="9643d-297">Отключение параметра **Проверка SSL-сертификата**</span><span class="sxs-lookup"><span data-stu-id="9643d-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="9643d-298">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="9643d-298">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="9643d-299">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="9643d-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="9643d-300">Тестирование PostTodoItem с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="9643d-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="9643d-301">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="9643d-301">Create a new request.</span></span>
* <span data-ttu-id="9643d-302">Установите HTTP-метод `POST`.</span><span class="sxs-lookup"><span data-stu-id="9643d-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="9643d-303">Задайте для URI значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="9643d-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="9643d-304">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="9643d-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="9643d-305">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="9643d-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="9643d-306">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="9643d-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="9643d-307">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="9643d-307">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="9643d-308">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="9643d-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="9643d-309">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-309">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="9643d-311">Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="9643d-311">Test the location header URI</span></span>

<span data-ttu-id="9643d-312">URI заголовка расположения можно протестировать в браузере.</span><span class="sxs-lookup"><span data-stu-id="9643d-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="9643d-313">Скопируйте и вставьте URI заголовка расположения в строку браузера.</span><span class="sxs-lookup"><span data-stu-id="9643d-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="9643d-314">Чтобы протестировать в Postman, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="9643d-314">To test in Postman:</span></span>

* <span data-ttu-id="9643d-315">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="9643d-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="9643d-316">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="9643d-316">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="9643d-318">Установите HTTP-метод `GET`.</span><span class="sxs-lookup"><span data-stu-id="9643d-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="9643d-319">Задайте для URI значение `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="9643d-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="9643d-320">Например, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="9643d-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="9643d-321">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-321">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="9643d-322">Знакомство с методами GET</span><span class="sxs-lookup"><span data-stu-id="9643d-322">Examine the GET methods</span></span>

<span data-ttu-id="9643d-323">Реализуются две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="9643d-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="9643d-324">Протестируйте приложение, вызвав эти две конечные точки в браузере или в Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="9643d-325">Пример:</span><span class="sxs-lookup"><span data-stu-id="9643d-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="9643d-326">При вызове `GetTodoItems` возвращается примерно такой ответ:</span><span class="sxs-lookup"><span data-stu-id="9643d-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="9643d-327">Тестирование Get с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="9643d-327">Test Get with Postman</span></span>

* <span data-ttu-id="9643d-328">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="9643d-328">Create a new request.</span></span>
* <span data-ttu-id="9643d-329">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="9643d-329">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="9643d-330">Задайте для URI запроса значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="9643d-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="9643d-331">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="9643d-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="9643d-332">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="9643d-333">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-333">Select **Send**.</span></span>

<span data-ttu-id="9643d-334">Это приложение использует выполняющуюся в памяти базу данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-334">This app uses an in-memory database.</span></span> <span data-ttu-id="9643d-335">Если остановить и вновь запустить его, предшествующий запрос GET не возвратит никаких данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="9643d-336">Если данные не возвращаются, данные для приложения получаются методом [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="9643d-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="9643d-337">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="9643d-337">Routing and URL paths</span></span>

<span data-ttu-id="9643d-338">Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="9643d-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="9643d-339">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="9643d-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="9643d-340">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="9643d-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="9643d-341">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="9643d-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="9643d-342">В этом примере класс контроллера имеет имя **TodoItems**, а сам контроллер, соответственно, — "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="9643d-342">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="9643d-343">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="9643d-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="9643d-344">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="9643d-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="9643d-345">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="9643d-345">This sample doesn't use a template.</span></span> <span data-ttu-id="9643d-346">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="9643d-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9643d-347">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="9643d-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="9643d-348">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="9643d-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="9643d-349">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="9643d-349">Return values</span></span>

<span data-ttu-id="9643d-350">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="9643d-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="9643d-351">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="9643d-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="9643d-352">Код ответа для этого типа возвращаемого значения равен [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="9643d-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="9643d-353">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="9643d-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="9643d-354">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="9643d-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="9643d-355">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="9643d-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="9643d-356">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает код ошибки [ 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.</span><span class="sxs-lookup"><span data-stu-id="9643d-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="9643d-357">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="9643d-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="9643d-358">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="9643d-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="9643d-359">Метод PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-359">The PutTodoItem method</span></span>

<span data-ttu-id="9643d-360">Проверьте метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="9643d-361">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="9643d-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="9643d-362">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="9643d-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="9643d-363">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="9643d-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="9643d-364">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="9643d-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="9643d-365">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="9643d-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="9643d-366">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="9643d-367">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="9643d-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="9643d-368">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="9643d-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="9643d-369">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="9643d-370">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя `"feed fish"`:</span><span class="sxs-lookup"><span data-stu-id="9643d-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="9643d-371">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="9643d-371">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="9643d-373">Метод DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="9643d-374">Проверьте метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="9643d-375">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="9643d-376">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="9643d-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="9643d-377">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="9643d-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="9643d-378">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="9643d-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="9643d-379">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-379">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="9643d-380">Предотвращение избыточной публикации</span><span class="sxs-lookup"><span data-stu-id="9643d-380">Prevent over-posting</span></span>

<span data-ttu-id="9643d-381">В настоящее время пример приложения предоставляет весь объект `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="9643d-382">Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели.</span><span class="sxs-lookup"><span data-stu-id="9643d-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="9643d-383">Это связано с несколькими причинами, и безопасность является основной.</span><span class="sxs-lookup"><span data-stu-id="9643d-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="9643d-384">Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления.</span><span class="sxs-lookup"><span data-stu-id="9643d-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="9643d-385">В этой статье используется **DTO**.</span><span class="sxs-lookup"><span data-stu-id="9643d-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="9643d-386">DTO можно использовать для следующего:</span><span class="sxs-lookup"><span data-stu-id="9643d-386">A DTO may be used to:</span></span>

* <span data-ttu-id="9643d-387">Предотвращение избыточной публикации.</span><span class="sxs-lookup"><span data-stu-id="9643d-387">Prevent over-posting.</span></span>
* <span data-ttu-id="9643d-388">Скрытие свойств, которые не предназначены для просмотра клиентами.</span><span class="sxs-lookup"><span data-stu-id="9643d-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="9643d-389">Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="9643d-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="9643d-390">Сведение графов объектов, содержащих вложенные объекты.</span><span class="sxs-lookup"><span data-stu-id="9643d-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="9643d-391">Сведенные графы объектов могут быть удобнее для клиентов.</span><span class="sxs-lookup"><span data-stu-id="9643d-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="9643d-392">Чтобы продемонстрировать подход с применением DTO, обновите класс `TodoItem`, включив в него поле секрета:</span><span class="sxs-lookup"><span data-stu-id="9643d-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="9643d-393">Поле секрета должно быть скрыто в этом приложении, однако административное приложение может отобразить его.</span><span class="sxs-lookup"><span data-stu-id="9643d-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="9643d-394">Убедитесь, что вы можете отправить и получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="9643d-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="9643d-395">Создайте модель DTO:</span><span class="sxs-lookup"><span data-stu-id="9643d-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="9643d-396">Обновите `TodoItemsController` для использования `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="9643d-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="9643d-397">Убедитесь, что вы можете отправить или получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="9643d-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="9643d-398">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="9643d-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="9643d-399">См. руководство по [: Вызовите веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="9643d-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="9643d-400">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="9643d-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9643d-401">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="9643d-401">Create a web API project.</span></span>
> * <span data-ttu-id="9643d-402">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="9643d-403">Формирование шаблонов контроллера с использованием методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="9643d-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="9643d-404">Настройка маршрутизации, URL-пути и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="9643d-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="9643d-405">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-405">Call the web API with Postman.</span></span>

<span data-ttu-id="9643d-406">В итоге вы получите веб-API, позволяющий работать с элементами списка дел, хранимыми в базе данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="9643d-407">Обзор</span><span class="sxs-lookup"><span data-stu-id="9643d-407">Overview</span></span>

<span data-ttu-id="9643d-408">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="9643d-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="9643d-409">API</span><span class="sxs-lookup"><span data-stu-id="9643d-409">API</span></span> | <span data-ttu-id="9643d-410">Описание</span><span class="sxs-lookup"><span data-stu-id="9643d-410">Description</span></span> | <span data-ttu-id="9643d-411">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="9643d-411">Request body</span></span> | <span data-ttu-id="9643d-412">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="9643d-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="9643d-413">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="9643d-413">Get all to-do items</span></span> | <span data-ttu-id="9643d-414">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-414">None</span></span> | <span data-ttu-id="9643d-415">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="9643d-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="9643d-416">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="9643d-416">Get an item by ID</span></span> | <span data-ttu-id="9643d-417">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-417">None</span></span> | <span data-ttu-id="9643d-418">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="9643d-419">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="9643d-419">Add a new item</span></span> | <span data-ttu-id="9643d-420">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-420">To-do item</span></span> | <span data-ttu-id="9643d-421">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="9643d-422">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9643d-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="9643d-423">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-423">To-do item</span></span> | <span data-ttu-id="9643d-424">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-424">None</span></span> |
|<span data-ttu-id="9643d-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9643d-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="9643d-426">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9643d-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="9643d-427">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-427">None</span></span> | <span data-ttu-id="9643d-428">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-428">None</span></span>|

<span data-ttu-id="9643d-429">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="9643d-429">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="9643d-435">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="9643d-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9643d-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9643d-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9643d-438">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="9643d-439">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="9643d-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9643d-441">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="9643d-441">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9643d-442">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="9643d-442">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="9643d-443">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="9643d-443">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="9643d-444">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="9643d-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="9643d-445">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="9643d-445">Select the **API** template and click **Create**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9643d-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9643d-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9643d-448">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9643d-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9643d-449">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="9643d-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="9643d-450">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="9643d-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="9643d-451">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="9643d-451">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="9643d-452">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="9643d-452">The preceding commands:</span></span>

  * <span data-ttu-id="9643d-453">Создает новый проект веб-API и открывает его в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9643d-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="9643d-454">Добавляет пакеты NuGet, которые понадобятся в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="9643d-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9643d-455">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9643d-456">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="9643d-456">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="9643d-458">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="9643d-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="9643d-459">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="9643d-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Выбор шаблона API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="9643d-461">В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **целевой платформы** выберите последнюю версию .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="9643d-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="9643d-462">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="9643d-462">Select **Next**.</span></span>

* <span data-ttu-id="9643d-463">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="9643d-463">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="9643d-465">Откройте командный терминал в папке проекта и выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="9643d-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="9643d-466">Тестирование API</span><span class="sxs-lookup"><span data-stu-id="9643d-466">Test the API</span></span>

<span data-ttu-id="9643d-467">Шаблон проекта создает API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="9643d-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="9643d-468">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="9643d-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9643d-470">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9643d-471">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/WeatherForecast`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="9643d-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="9643d-472">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="9643d-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="9643d-473">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="9643d-473">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9643d-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9643d-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9643d-475">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9643d-476">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="9643d-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9643d-477">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9643d-478">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="9643d-479">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="9643d-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="9643d-480">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="9643d-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="9643d-481">Добавьте `/WeatherForecast` к URL-адресу (измените URL-адрес на `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="9643d-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="9643d-482">Возвращаемые данные JSON будут выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="9643d-482">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="9643d-483">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="9643d-483">Add a model class</span></span>

<span data-ttu-id="9643d-484">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="9643d-485">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9643d-487">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="9643d-487">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="9643d-488">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="9643d-488">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9643d-489">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-489">Name the folder *Models*.</span></span>

* <span data-ttu-id="9643d-490">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="9643d-490">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9643d-491">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-491">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="9643d-492">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9643d-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9643d-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9643d-494">Добавьте папку с именем *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-494">Add a folder named *Models*.</span></span>

* <span data-ttu-id="9643d-495">Добавьте в папку *Models* класс `TodoItem` со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-495">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9643d-496">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9643d-497">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="9643d-497">Right-click the project.</span></span> <span data-ttu-id="9643d-498">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="9643d-498">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9643d-499">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-499">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="9643d-501">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="9643d-501">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="9643d-502">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="9643d-502">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="9643d-503">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="9643d-504">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="9643d-505">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-505">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="9643d-506">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="9643d-506">Add a database context</span></span>

<span data-ttu-id="9643d-507">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="9643d-508">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="9643d-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="9643d-510">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="9643d-510">Add NuGet packages</span></span>

* <span data-ttu-id="9643d-511">В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.</span><span class="sxs-lookup"><span data-stu-id="9643d-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="9643d-512">Перейдите на вкладку **Обзор** и введите **Microsoft.EntityFrameworkCore.SqlServer** в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="9643d-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="9643d-513">На панели слева выберите **Microsoft.EntityFrameworkCore.SqlServer**.</span><span class="sxs-lookup"><span data-stu-id="9643d-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="9643d-514">Установите флажок **Проект** на правой панели и выберите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-514">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="9643d-515">Добавьте пакет NuGet **пакет NuGet Microsoft.EntityFrameworkCore.InMemory**, выполнив приведенные выше инструкции.</span><span class="sxs-lookup"><span data-stu-id="9643d-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Диспетчер пакетов NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="9643d-517">Добавление контекста базы данных TodoContext</span><span class="sxs-lookup"><span data-stu-id="9643d-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="9643d-518">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="9643d-518">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9643d-519">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-519">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9643d-520">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9643d-521">Добавьте класс `TodoContext` в папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-521">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="9643d-522">Введите следующий код:</span><span class="sxs-lookup"><span data-stu-id="9643d-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="9643d-523">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="9643d-523">Register the database context</span></span>

<span data-ttu-id="9643d-524">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9643d-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9643d-525">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="9643d-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="9643d-526">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="9643d-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="9643d-527">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="9643d-527">The preceding code:</span></span>

* <span data-ttu-id="9643d-528">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="9643d-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="9643d-529">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="9643d-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="9643d-530">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="9643d-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="9643d-531">Формирование шаблонов контроллера</span><span class="sxs-lookup"><span data-stu-id="9643d-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9643d-533">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="9643d-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="9643d-534">Щелкните **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="9643d-534">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="9643d-535">Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-535">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="9643d-536">В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="9643d-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="9643d-537">Выберите **TodoItem (TodoApi.Models)** в поле **Класс модели**.</span><span class="sxs-lookup"><span data-stu-id="9643d-537">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="9643d-538">Выберите **TodoContext (TodoApi.Models)** в поле **Класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="9643d-538">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="9643d-539">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-539">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9643d-540">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9643d-541">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="9643d-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="9643d-542">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="9643d-542">The preceding commands:</span></span>

* <span data-ttu-id="9643d-543">добавляют пакеты NuGet, необходимые для формирования шаблонов;</span><span class="sxs-lookup"><span data-stu-id="9643d-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="9643d-544">устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);</span><span class="sxs-lookup"><span data-stu-id="9643d-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="9643d-545">формируют шаблоны для `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="9643d-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="9643d-546">Сформированный код:</span><span class="sxs-lookup"><span data-stu-id="9643d-546">The generated code:</span></span>

* <span data-ttu-id="9643d-547">Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="9643d-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="9643d-548">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="9643d-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="9643d-549">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="9643d-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="9643d-550">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="9643d-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="9643d-551">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="9643d-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="9643d-552">Шаблоны ASP.NET Core для:</span><span class="sxs-lookup"><span data-stu-id="9643d-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="9643d-553">Контроллеры с представлениями включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="9643d-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="9643d-554">Контроллеры API не включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="9643d-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="9643d-555">Если токен `[action]` не находится в шаблоне маршрута, имя [действия](xref:mvc/controllers/routing#action) исключается из маршрута.</span><span class="sxs-lookup"><span data-stu-id="9643d-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="9643d-556">То есть имя связанного метода действия не используется в соответствующем маршруте.</span><span class="sxs-lookup"><span data-stu-id="9643d-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="9643d-557">Знакомство с методом создания PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="9643d-558">Измените инструкцию возврата в `PostTodoItem` и используйте оператор [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="9643d-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="9643d-559">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="9643d-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="9643d-560">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="9643d-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="9643d-561">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="9643d-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9643d-562">Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="9643d-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="9643d-563">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="9643d-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="9643d-564">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="9643d-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="9643d-565">Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location).</span><span class="sxs-lookup"><span data-stu-id="9643d-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="9643d-566">Заголовок `Location` указывает [URI](https://developer.mozilla.org/docs/Glossary/URI) новой созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="9643d-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="9643d-567">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="9643d-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="9643d-568">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="9643d-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="9643d-569">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="9643d-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="9643d-570">Установка Postman</span><span class="sxs-lookup"><span data-stu-id="9643d-570">Install Postman</span></span>

<span data-ttu-id="9643d-571">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="9643d-572">Установка [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="9643d-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="9643d-573">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-573">Start the web app.</span></span>
* <span data-ttu-id="9643d-574">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-574">Start Postman.</span></span>
* <span data-ttu-id="9643d-575">Отключение параметра **Проверка SSL-сертификата**</span><span class="sxs-lookup"><span data-stu-id="9643d-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="9643d-576">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="9643d-576">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="9643d-577">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="9643d-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="9643d-578">Тестирование PostTodoItem с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="9643d-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="9643d-579">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="9643d-579">Create a new request.</span></span>
* <span data-ttu-id="9643d-580">Установите HTTP-метод `POST`.</span><span class="sxs-lookup"><span data-stu-id="9643d-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="9643d-581">Задайте для URI значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="9643d-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="9643d-582">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="9643d-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="9643d-583">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="9643d-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="9643d-584">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="9643d-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="9643d-585">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="9643d-585">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="9643d-586">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="9643d-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="9643d-587">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-587">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="9643d-589">Тестирование URI заголовка расположения с помощью Postman</span><span class="sxs-lookup"><span data-stu-id="9643d-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="9643d-590">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="9643d-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="9643d-591">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="9643d-591">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="9643d-593">Установите HTTP-метод `GET`.</span><span class="sxs-lookup"><span data-stu-id="9643d-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="9643d-594">Задайте для URI значение `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="9643d-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="9643d-595">Например, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="9643d-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="9643d-596">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-596">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="9643d-597">Знакомство с методами GET</span><span class="sxs-lookup"><span data-stu-id="9643d-597">Examine the GET methods</span></span>

<span data-ttu-id="9643d-598">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="9643d-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="9643d-599">Протестируйте приложение, вызвав эти две конечные точки в браузере или в Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="9643d-600">Пример:</span><span class="sxs-lookup"><span data-stu-id="9643d-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="9643d-601">При вызове `GetTodoItems` возвращается примерно такой ответ:</span><span class="sxs-lookup"><span data-stu-id="9643d-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="9643d-602">Тестирование Get с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="9643d-602">Test Get with Postman</span></span>

* <span data-ttu-id="9643d-603">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="9643d-603">Create a new request.</span></span>
* <span data-ttu-id="9643d-604">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="9643d-604">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="9643d-605">Задайте для URI запроса значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="9643d-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="9643d-606">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="9643d-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="9643d-607">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="9643d-608">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-608">Select **Send**.</span></span>

<span data-ttu-id="9643d-609">Это приложение использует выполняющуюся в памяти базу данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-609">This app uses an in-memory database.</span></span> <span data-ttu-id="9643d-610">Если остановить и вновь запустить его, предшествующий запрос GET не возвратит никаких данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="9643d-611">Если данные не возвращаются, данные для приложения получаются методом [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="9643d-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="9643d-612">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="9643d-612">Routing and URL paths</span></span>

<span data-ttu-id="9643d-613">Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="9643d-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="9643d-614">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="9643d-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="9643d-615">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="9643d-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="9643d-616">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="9643d-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="9643d-617">В этом примере класс контроллера имеет имя **TodoItems**, а сам контроллер, соответственно, — "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="9643d-617">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="9643d-618">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="9643d-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="9643d-619">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="9643d-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="9643d-620">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="9643d-620">This sample doesn't use a template.</span></span> <span data-ttu-id="9643d-621">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="9643d-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9643d-622">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="9643d-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="9643d-623">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="9643d-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="9643d-624">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="9643d-624">Return values</span></span> 

<span data-ttu-id="9643d-625">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="9643d-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="9643d-626">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="9643d-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="9643d-627">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="9643d-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="9643d-628">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="9643d-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="9643d-629">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="9643d-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="9643d-630">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="9643d-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="9643d-631">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>).</span><span class="sxs-lookup"><span data-stu-id="9643d-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="9643d-632">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="9643d-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="9643d-633">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="9643d-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="9643d-634">Метод PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-634">The PutTodoItem method</span></span>

<span data-ttu-id="9643d-635">Проверьте метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="9643d-636">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="9643d-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="9643d-637">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="9643d-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="9643d-638">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="9643d-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="9643d-639">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="9643d-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="9643d-640">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="9643d-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="9643d-641">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="9643d-642">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="9643d-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="9643d-643">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="9643d-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="9643d-644">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="9643d-645">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="9643d-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="9643d-646">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="9643d-646">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="9643d-648">Метод DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="9643d-649">Проверьте метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="9643d-650">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="9643d-651">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="9643d-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="9643d-652">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="9643d-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="9643d-653">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="9643d-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="9643d-654">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-654">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="9643d-655">Предотвращение избыточной публикации</span><span class="sxs-lookup"><span data-stu-id="9643d-655">Prevent over-posting</span></span>

<span data-ttu-id="9643d-656">В настоящее время пример приложения предоставляет весь объект `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="9643d-657">Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели.</span><span class="sxs-lookup"><span data-stu-id="9643d-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="9643d-658">Это связано с несколькими причинами, и безопасность является основной.</span><span class="sxs-lookup"><span data-stu-id="9643d-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="9643d-659">Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления.</span><span class="sxs-lookup"><span data-stu-id="9643d-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="9643d-660">В этой статье используется **DTO**.</span><span class="sxs-lookup"><span data-stu-id="9643d-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="9643d-661">DTO можно использовать для следующего:</span><span class="sxs-lookup"><span data-stu-id="9643d-661">A DTO may be used to:</span></span>

* <span data-ttu-id="9643d-662">Предотвращение избыточной публикации.</span><span class="sxs-lookup"><span data-stu-id="9643d-662">Prevent over-posting.</span></span>
* <span data-ttu-id="9643d-663">Скрытие свойств, которые не предназначены для просмотра клиентами.</span><span class="sxs-lookup"><span data-stu-id="9643d-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="9643d-664">Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="9643d-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="9643d-665">Сведение графов объектов, содержащих вложенные объекты.</span><span class="sxs-lookup"><span data-stu-id="9643d-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="9643d-666">Сведенные графы объектов могут быть удобнее для клиентов.</span><span class="sxs-lookup"><span data-stu-id="9643d-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="9643d-667">Чтобы продемонстрировать подход с применением DTO, обновите класс `TodoItem`, включив в него поле секрета:</span><span class="sxs-lookup"><span data-stu-id="9643d-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="9643d-668">Поле секрета должно быть скрыто в этом приложении, однако административное приложение может отобразить его.</span><span class="sxs-lookup"><span data-stu-id="9643d-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="9643d-669">Убедитесь, что вы можете отправить и получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="9643d-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="9643d-670">Создайте модель DTO:</span><span class="sxs-lookup"><span data-stu-id="9643d-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="9643d-671">Обновите `TodoItemsController` для использования `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="9643d-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="9643d-672">Убедитесь, что вы можете отправить или получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="9643d-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="9643d-673">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="9643d-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="9643d-674">См. руководство по [: Вызовите веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="9643d-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9643d-675">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="9643d-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9643d-676">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="9643d-676">Create a web API project.</span></span>
> * <span data-ttu-id="9643d-677">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="9643d-678">Добавление контроллера.</span><span class="sxs-lookup"><span data-stu-id="9643d-678">Add a controller.</span></span>
> * <span data-ttu-id="9643d-679">Добавление методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="9643d-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="9643d-680">Настройка маршрутизации и путей URL.</span><span class="sxs-lookup"><span data-stu-id="9643d-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="9643d-681">Указание возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="9643d-681">Specify return values.</span></span>
> * <span data-ttu-id="9643d-682">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="9643d-683">Вызовите веб-API с помощью JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9643d-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="9643d-684">В конечном итоге вы получите веб-API, обеспечивающий управление элементами списка дел, хранящимися в реляционной базе данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="9643d-685">2\.1. Обзор</span><span class="sxs-lookup"><span data-stu-id="9643d-685">Overview 2.1</span></span>

<span data-ttu-id="9643d-686">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="9643d-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="9643d-687">API</span><span class="sxs-lookup"><span data-stu-id="9643d-687">API</span></span> | <span data-ttu-id="9643d-688">Описание</span><span class="sxs-lookup"><span data-stu-id="9643d-688">Description</span></span> | <span data-ttu-id="9643d-689">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="9643d-689">Request body</span></span> | <span data-ttu-id="9643d-690">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="9643d-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="9643d-691">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="9643d-691">GET /api/TodoItems</span></span> | <span data-ttu-id="9643d-692">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="9643d-692">Get all to-do items</span></span> | <span data-ttu-id="9643d-693">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-693">None</span></span> | <span data-ttu-id="9643d-694">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="9643d-694">Array of to-do items</span></span>|
|<span data-ttu-id="9643d-695">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="9643d-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="9643d-696">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="9643d-696">Get an item by ID</span></span> | <span data-ttu-id="9643d-697">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-697">None</span></span> | <span data-ttu-id="9643d-698">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-698">To-do item</span></span>|
|<span data-ttu-id="9643d-699">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="9643d-699">POST /api/TodoItems</span></span> | <span data-ttu-id="9643d-700">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="9643d-700">Add a new item</span></span> | <span data-ttu-id="9643d-701">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-701">To-do item</span></span> | <span data-ttu-id="9643d-702">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-702">To-do item</span></span> |
|<span data-ttu-id="9643d-703">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="9643d-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="9643d-704">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9643d-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="9643d-705">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-705">To-do item</span></span> | <span data-ttu-id="9643d-706">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-706">None</span></span> |
|<span data-ttu-id="9643d-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9643d-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="9643d-708">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9643d-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="9643d-709">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-709">None</span></span> | <span data-ttu-id="9643d-710">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="9643d-710">None</span></span>|

<span data-ttu-id="9643d-711">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="9643d-711">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="9643d-717">2\.1. Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="9643d-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9643d-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9643d-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9643d-720">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="9643d-721">2\.1. Создание веб-проекта</span><span class="sxs-lookup"><span data-stu-id="9643d-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9643d-723">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="9643d-723">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9643d-724">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="9643d-724">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="9643d-725">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="9643d-725">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="9643d-726">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="9643d-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="9643d-727">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="9643d-727">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="9643d-728">**Не** выбирайте **Включение поддержки Docker**.</span><span class="sxs-lookup"><span data-stu-id="9643d-728">**Don't** select **Enable Docker Support**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9643d-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9643d-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9643d-731">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9643d-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9643d-732">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="9643d-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="9643d-733">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="9643d-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="9643d-734">С помощью этих команд создается новый проект веб-API и открывается новый экземпляр Visual Studio Code в новой папке проекта.</span><span class="sxs-lookup"><span data-stu-id="9643d-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="9643d-735">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="9643d-735">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9643d-736">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9643d-737">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="9643d-737">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="9643d-739">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="9643d-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="9643d-740">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="9643d-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="9643d-741">В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **целевой платформы** выберите последнюю версию .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="9643d-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="9643d-742">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="9643d-742">Select **Next**.</span></span>

* <span data-ttu-id="9643d-743">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="9643d-743">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="9643d-745">2\.1. Тестирование API</span><span class="sxs-lookup"><span data-stu-id="9643d-745">Test the API 2.1</span></span>

<span data-ttu-id="9643d-746">Шаблон проекта создает API `values`.</span><span class="sxs-lookup"><span data-stu-id="9643d-746">The project template creates a `values` API.</span></span> <span data-ttu-id="9643d-747">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="9643d-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9643d-749">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9643d-750">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/api/values`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="9643d-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="9643d-751">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="9643d-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="9643d-752">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="9643d-752">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9643d-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9643d-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9643d-754">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9643d-755">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="9643d-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9643d-756">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9643d-757">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="9643d-758">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="9643d-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="9643d-759">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="9643d-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="9643d-760">Добавьте `/api/values` к URL-адресу (измените URL-адрес на `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="9643d-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="9643d-761">Возвращается следующий файл JSON:</span><span class="sxs-lookup"><span data-stu-id="9643d-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="9643d-762">2\.1. Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="9643d-762">Add a model class 2.1</span></span>

<span data-ttu-id="9643d-763">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="9643d-764">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9643d-766">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="9643d-766">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="9643d-767">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="9643d-767">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9643d-768">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-768">Name the folder *Models*.</span></span>

* <span data-ttu-id="9643d-769">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="9643d-769">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9643d-770">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-770">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="9643d-771">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9643d-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9643d-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9643d-773">Добавьте папку с именем *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-773">Add a folder named *Models*.</span></span>

* <span data-ttu-id="9643d-774">Добавьте в папку *Models* класс `TodoItem` со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-774">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9643d-775">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9643d-776">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="9643d-776">Right-click the project.</span></span> <span data-ttu-id="9643d-777">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="9643d-777">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9643d-778">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-778">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="9643d-780">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="9643d-780">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="9643d-781">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="9643d-781">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="9643d-782">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="9643d-783">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="9643d-784">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-784">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="9643d-785">2\.1. Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="9643d-785">Add a database context 2.1</span></span>

<span data-ttu-id="9643d-786">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="9643d-787">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="9643d-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9643d-789">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="9643d-789">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9643d-790">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-790">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9643d-791">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9643d-792">Добавьте класс `TodoContext` в папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="9643d-792">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="9643d-793">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="9643d-794">2\.1. Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="9643d-794">Register the database context 2.1</span></span>

<span data-ttu-id="9643d-795">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9643d-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9643d-796">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="9643d-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="9643d-797">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="9643d-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="9643d-798">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="9643d-798">The preceding code:</span></span>

* <span data-ttu-id="9643d-799">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="9643d-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="9643d-800">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="9643d-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="9643d-801">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="9643d-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="9643d-802">2\.1. Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="9643d-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9643d-804">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="9643d-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="9643d-805">Щелкните **Добавить** > **Создать элемент**.</span><span class="sxs-lookup"><span data-stu-id="9643d-805">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="9643d-806">В диалоговом окне **Добавить новый элемент** выберите шаблон **Класс контроллера API**.</span><span class="sxs-lookup"><span data-stu-id="9643d-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="9643d-807">Присвойте классу имя *TodoController* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-807">Name the class *TodoController*, and select **Add**.</span></span>

  ![Диалоговое окно добавления элемента с контроллером в поле поиска и выбранным контроллером веб-API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9643d-809">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9643d-810">В папке *Controllers* создайте класс с именем `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="9643d-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="9643d-811">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="9643d-812">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="9643d-812">The preceding code:</span></span>

* <span data-ttu-id="9643d-813">Определяет класс контроллера API без методов.</span><span class="sxs-lookup"><span data-stu-id="9643d-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="9643d-814">Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="9643d-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="9643d-815">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="9643d-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="9643d-816">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="9643d-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="9643d-817">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="9643d-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="9643d-818">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="9643d-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="9643d-819">Добавляет элемент `Item1` в базу данных, если она пуста.</span><span class="sxs-lookup"><span data-stu-id="9643d-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="9643d-820">Этот код находится в конструкторе и выполняется каждый раз при обнаружении нового HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="9643d-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="9643d-821">Если вы удалите все элементы, конструктор создаст `Item1` при следующем вызове метода API.</span><span class="sxs-lookup"><span data-stu-id="9643d-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="9643d-822">Поэтому может создаться впечатление, что удаление не было выполнено, хотя это не так.</span><span class="sxs-lookup"><span data-stu-id="9643d-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="9643d-823">2\.1. Добавление методов Get</span><span class="sxs-lookup"><span data-stu-id="9643d-823">Add Get methods 2.1</span></span>

<span data-ttu-id="9643d-824">Чтобы получить API, который извлекает элементы списка дел, добавьте следующие методы в класс `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="9643d-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="9643d-825">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="9643d-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="9643d-826">Если приложение все еще выполняется, оно останавливается.</span><span class="sxs-lookup"><span data-stu-id="9643d-826">Stop the app if it's still running.</span></span> <span data-ttu-id="9643d-827">Затем оно запускается снова с последними изменениями.</span><span class="sxs-lookup"><span data-stu-id="9643d-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="9643d-828">Протестируйте приложение, вызвав эти две конечные точки в браузере.</span><span class="sxs-lookup"><span data-stu-id="9643d-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="9643d-829">Пример:</span><span class="sxs-lookup"><span data-stu-id="9643d-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="9643d-830">При вызове `GetTodoItems` возвращается следующий ответ HTTP:</span><span class="sxs-lookup"><span data-stu-id="9643d-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="9643d-831">2\.1. Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="9643d-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="9643d-832">Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="9643d-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="9643d-833">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="9643d-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="9643d-834">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="9643d-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="9643d-835">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="9643d-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="9643d-836">В этом примере класс контроллера носит имя **Todo**, а сам контроллер, соответственно, — "todo".</span><span class="sxs-lookup"><span data-stu-id="9643d-836">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="9643d-837">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="9643d-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="9643d-838">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="9643d-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="9643d-839">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="9643d-839">This sample doesn't use a template.</span></span> <span data-ttu-id="9643d-840">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="9643d-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9643d-841">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="9643d-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="9643d-842">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="9643d-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="9643d-843">2\.1. Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="9643d-843">Return values 2.1</span></span>

<span data-ttu-id="9643d-844">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="9643d-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="9643d-845">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="9643d-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="9643d-846">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="9643d-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="9643d-847">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="9643d-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="9643d-848">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="9643d-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="9643d-849">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="9643d-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="9643d-850">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>).</span><span class="sxs-lookup"><span data-stu-id="9643d-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="9643d-851">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="9643d-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="9643d-852">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="9643d-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="9643d-853">2\.1. Тестирование метода GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="9643d-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="9643d-854">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="9643d-855">Установите [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="9643d-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="9643d-856">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="9643d-856">Start the web app.</span></span>
* <span data-ttu-id="9643d-857">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-857">Start Postman.</span></span>
* <span data-ttu-id="9643d-858">Отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="9643d-858">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9643d-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9643d-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9643d-860">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="9643d-860">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9643d-861">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9643d-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9643d-862">В меню **Postman** > **Настройки** (вкладка **Общие**) отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="9643d-862">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="9643d-863">Кроме того, можно выбрать значок гаечного ключа и выбрать **Параметры**, а затем отключить проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="9643d-863">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="9643d-864">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="9643d-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="9643d-865">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="9643d-865">Create a new request.</span></span>
  * <span data-ttu-id="9643d-866">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="9643d-866">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="9643d-867">Задайте для URI запроса значение `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="9643d-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="9643d-868">Например, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="9643d-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="9643d-869">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="9643d-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="9643d-870">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-870">Select **Send**.</span></span>

![Postman с запросом Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="9643d-872">2\.1. Добавление метода Create</span><span class="sxs-lookup"><span data-stu-id="9643d-872">Add a Create method 2.1</span></span>

<span data-ttu-id="9643d-873">Добавьте следующий метод `PostTodoItem` в *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="9643d-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="9643d-874">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="9643d-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="9643d-875">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="9643d-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="9643d-876">Метод `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="9643d-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="9643d-877">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="9643d-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="9643d-878">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="9643d-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="9643d-879">Добавляет заголовок `Location` в ответ.</span><span class="sxs-lookup"><span data-stu-id="9643d-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="9643d-880">Заголовок `Location` расположения указывает URI вновь созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="9643d-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="9643d-881">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="9643d-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="9643d-882">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="9643d-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="9643d-883">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="9643d-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="9643d-884">2\.1. Тестирование метода PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="9643d-885">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="9643d-885">Build the project.</span></span>
* <span data-ttu-id="9643d-886">В Postman укажите метод HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="9643d-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="9643d-887">Задайте для URI значение `https://localhost:<port>/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-887">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="9643d-888">Например, `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-888">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="9643d-889">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="9643d-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="9643d-890">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="9643d-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="9643d-891">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="9643d-891">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="9643d-892">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="9643d-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="9643d-893">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-893">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/create.png)

  <span data-ttu-id="9643d-895">Если вы получаете ошибку 405 "Недопустимый метод", это может свидетельствовать о том, что после добавления метода `PostTodoItem` не была выполнена компиляция проекта.</span><span class="sxs-lookup"><span data-stu-id="9643d-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="9643d-896">2\.1. Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="9643d-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="9643d-897">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="9643d-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="9643d-898">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="9643d-898">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="9643d-900">Укажите метод GET.</span><span class="sxs-lookup"><span data-stu-id="9643d-900">Set the method to GET.</span></span>
<span data-ttu-id="9643d-901">\* Задайте для URI значение  `https://localhost:<port>/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="9643d-901">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="9643d-902"> Например:  `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="9643d-902"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="9643d-903">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-903">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="9643d-904">2\.1. Добавление метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="9643d-905">Добавьте приведенный ниже метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="9643d-906">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="9643d-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="9643d-907">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="9643d-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="9643d-908">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="9643d-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="9643d-909">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="9643d-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="9643d-910">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="9643d-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="9643d-911">2\.1. Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="9643d-912">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="9643d-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="9643d-913">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="9643d-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="9643d-914">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="9643d-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="9643d-915">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="9643d-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="9643d-916">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="9643d-916">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="9643d-918">2\.1. Добавление метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="9643d-919">Добавьте приведенный ниже метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9643d-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="9643d-920">`DeleteTodoItem`Ответ[ — 204 (нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="9643d-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="9643d-921">2\.1. Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="9643d-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="9643d-922">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="9643d-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="9643d-923">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="9643d-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="9643d-924">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="9643d-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="9643d-925">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="9643d-925">Select **Send**.</span></span>

<span data-ttu-id="9643d-926">В этом примере приложения вы можете удалить все элементы.</span><span class="sxs-lookup"><span data-stu-id="9643d-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="9643d-927">Однако в случае удаления последнего элемента в момент следующего вызова API конструктор класса модели создаст новый элемент.</span><span class="sxs-lookup"><span data-stu-id="9643d-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="9643d-928">2\.1. Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="9643d-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="9643d-929">В этом разделе описано, как добавить HTML-страницу, которая использует JavaScript для вызова веб-API.</span><span class="sxs-lookup"><span data-stu-id="9643d-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="9643d-930">jQuery инициирует запрос.</span><span class="sxs-lookup"><span data-stu-id="9643d-930">jQuery initiates the request.</span></span> <span data-ttu-id="9643d-931">JavaScript изменяет страницу, используя сведения из ответа API.</span><span class="sxs-lookup"><span data-stu-id="9643d-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="9643d-932">Настройте приложение для [обслуживания статических файлов](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) и [включения сопоставления файлов по умолчанию](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A), обновив *Startup.cs* следующим выделенным кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="9643d-933">Создайте папку *wwwroot* в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="9643d-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="9643d-934">Добавьте HTML-файл *index.html* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="9643d-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="9643d-935">Замените его содержимое следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="9643d-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="9643d-936">Добавьте файл JavaScript с именем *site.js* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="9643d-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="9643d-937">Замените его содержимое следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="9643d-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="9643d-938">Может потребоваться изменение параметров запуска проекта ASP.NET Core для локального тестирования HTML-страницы:</span><span class="sxs-lookup"><span data-stu-id="9643d-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="9643d-939">Откройте файл *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9643d-939">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="9643d-940">Удалите свойство `launchUrl`, чтобы приложение открылось через *index.html* &mdash; файл проекта по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="9643d-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="9643d-941">В этом примере вызываются все методы CRUD в веб-API.</span><span class="sxs-lookup"><span data-stu-id="9643d-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="9643d-942">Ниже приводится пояснение вызовов API.</span><span class="sxs-lookup"><span data-stu-id="9643d-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="9643d-943">2\.1. Получение списка элементов задач</span><span class="sxs-lookup"><span data-stu-id="9643d-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="9643d-944">jQuery отправляет запрос HTTP GET к веб-API, который возвращает ответ JSON, представляющий массив элементов списка дел.</span><span class="sxs-lookup"><span data-stu-id="9643d-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="9643d-945">В случае успешного запроса используется функция обратного вызова `success`.</span><span class="sxs-lookup"><span data-stu-id="9643d-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="9643d-946">При обратном вызове в модель DOM вносятся данные о задачах.</span><span class="sxs-lookup"><span data-stu-id="9643d-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="9643d-947">2\.1. Добавление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="9643d-948">jQuery отправляет запрос HTTP POST с элементом списка дел в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="9643d-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="9643d-949">Для параметров `accepts` и `contentType` указывается `application/json`, чтобы указать тип носителя при получении и отправке.</span><span class="sxs-lookup"><span data-stu-id="9643d-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="9643d-950">Элемент списка дел преобразуется в JSON с помощью [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="9643d-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="9643d-951">Если интерфейс API возвращает код состояния успешного выполнения, вызывается функция `getData` для обновления HTML-таблицы.</span><span class="sxs-lookup"><span data-stu-id="9643d-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="9643d-952">2\.1. Обновление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="9643d-953">Обновление элемента списка дел выполняется аналогично его добавлению.</span><span class="sxs-lookup"><span data-stu-id="9643d-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="9643d-954">`url` изменяется, чтобы добавить уникальный идентификатор для элемента, а в качестве `type` устанавливается `PUT`.</span><span class="sxs-lookup"><span data-stu-id="9643d-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="9643d-955">2\.1. Удаление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="9643d-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="9643d-956">Чтобы удалить элемент списка дел, установите для `type` в вызове AJAX значение `DELETE` и укажите уникальный идентификатор элемента в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="9643d-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="9643d-957">2\.1. Добавление поддержки аутентификации в веб-API</span><span class="sxs-lookup"><span data-stu-id="9643d-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="9643d-958">2\.1. Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9643d-958">Additional resources 2.1</span></span>

<span data-ttu-id="9643d-959">[Просмотреть или скачать пример кода для этого учебника](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="9643d-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="9643d-960">См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="9643d-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="9643d-961">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="9643d-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="9643d-962">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="9643d-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
