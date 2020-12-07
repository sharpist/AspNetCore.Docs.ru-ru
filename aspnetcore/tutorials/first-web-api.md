---
title: Учебник. Создание веб-API с помощью ASP.NET Core
author: rick-anderson
description: Узнайте, как создать веб-API с помощью ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: ccbfc27eb89e23938a69f0ab4cb306d6a4136889
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175056"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="c731f-103">Учебник. Создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c731f-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="c731f-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Майк Уоссон](https://github.com/mikewasson) (Mike Wasson)</span><span class="sxs-lookup"><span data-stu-id="c731f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="c731f-105">В этом учебнике приводятся основные сведения о создании веб-API с помощью ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c731f-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c731f-106">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="c731f-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c731f-107">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="c731f-107">Create a web API project.</span></span>
> * <span data-ttu-id="c731f-108">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c731f-109">Формирование шаблонов контроллера с использованием методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="c731f-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="c731f-110">Настройка маршрутизации, URL-пути и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="c731f-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="c731f-111">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-111">Call the web API with Postman.</span></span>

<span data-ttu-id="c731f-112">В итоге вы получите веб-API, позволяющий работать с элементами списка дел, хранимыми в базе данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="c731f-113">Обзор</span><span class="sxs-lookup"><span data-stu-id="c731f-113">Overview</span></span>

<span data-ttu-id="c731f-114">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="c731f-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c731f-115">API</span><span class="sxs-lookup"><span data-stu-id="c731f-115">API</span></span> | <span data-ttu-id="c731f-116">Описание</span><span class="sxs-lookup"><span data-stu-id="c731f-116">Description</span></span> | <span data-ttu-id="c731f-117">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="c731f-117">Request body</span></span> | <span data-ttu-id="c731f-118">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="c731f-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="c731f-119">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="c731f-119">Get all to-do items</span></span> | <span data-ttu-id="c731f-120">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-120">None</span></span> | <span data-ttu-id="c731f-121">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="c731f-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="c731f-122">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="c731f-122">Get an item by ID</span></span> | <span data-ttu-id="c731f-123">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-123">None</span></span> | <span data-ttu-id="c731f-124">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="c731f-125">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="c731f-125">Add a new item</span></span> | <span data-ttu-id="c731f-126">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-126">To-do item</span></span> | <span data-ttu-id="c731f-127">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="c731f-128">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c731f-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c731f-129">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-129">To-do item</span></span> | <span data-ttu-id="c731f-130">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-130">None</span></span> |
|<span data-ttu-id="c731f-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c731f-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="c731f-132">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c731f-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c731f-133">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-133">None</span></span> | <span data-ttu-id="c731f-134">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-134">None</span></span>|

<span data-ttu-id="c731f-135">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="c731f-135">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="c731f-141">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="c731f-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c731f-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c731f-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c731f-144">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="c731f-145">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="c731f-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c731f-147">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="c731f-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c731f-148">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c731f-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="c731f-149">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c731f-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="c731f-150">Убедитесь, что в диалоговом окне **Создание веб-приложения ASP.NET Core** выбраны платформы **.NET Core** и **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="c731f-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="c731f-151">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c731f-151">Select the **API** template and click **Create**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c731f-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c731f-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c731f-154">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c731f-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c731f-155">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="c731f-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c731f-156">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c731f-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="c731f-157">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c731f-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="c731f-158">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="c731f-158">The preceding commands:</span></span>

  * <span data-ttu-id="c731f-159">Создает новый проект веб-API и открывает его в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c731f-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="c731f-160">Добавляет пакеты NuGet, которые понадобятся в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="c731f-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c731f-161">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c731f-162">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="c731f-162">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c731f-164">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c731f-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="c731f-165">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c731f-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Выбор шаблона API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="c731f-167">В диалоговом окне **Configure the new ASP.NET Core Web API** (Настройка нового веб-API ASP.NET Core) в качестве **целевой платформы** выберите последнюю версию .NET Core 5.x.</span><span class="sxs-lookup"><span data-stu-id="c731f-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="c731f-168">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c731f-168">Select **Next**.</span></span>

* <span data-ttu-id="c731f-169">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c731f-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="c731f-171">Откройте командный терминал в папке проекта и выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c731f-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="c731f-172">Тестирование проекта</span><span class="sxs-lookup"><span data-stu-id="c731f-172">Test the project</span></span>

<span data-ttu-id="c731f-173">Шаблон проекта создает API `WeatherForecast` с поддержкой [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="c731f-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c731f-175">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="c731f-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="c731f-176">Visual Studio запустит:</span><span class="sxs-lookup"><span data-stu-id="c731f-176">Visual Studio launches:</span></span>

* <span data-ttu-id="c731f-177">веб-сервер IIS Express;</span><span class="sxs-lookup"><span data-stu-id="c731f-177">The IIS Express web server.</span></span>
* <span data-ttu-id="c731f-178">браузер по умолчанию и перейдет к `https://localhost:<port>/swagger/index.html`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="c731f-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c731f-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c731f-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="c731f-180">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c731f-181">В браузере перейдите по следующему URL-адресу: [https://localhost:5001/swagger](https://localhost:5001/swagger).</span><span class="sxs-lookup"><span data-stu-id="c731f-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c731f-182">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c731f-183">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c731f-184">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="c731f-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c731f-185">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="c731f-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c731f-186">Добавьте `/swagger` к URL-адресу (измените URL-адрес на `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="c731f-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="c731f-187">Откроется страница Swagger `/swagger/index.html`.</span><span class="sxs-lookup"><span data-stu-id="c731f-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="c731f-188">Выберите **Get** (Получить) > **Try it out** (Попробовать) > **Execute** (Выполнить).</span><span class="sxs-lookup"><span data-stu-id="c731f-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="c731f-189">На странице отобразятся:</span><span class="sxs-lookup"><span data-stu-id="c731f-189">The page displays:</span></span>

* <span data-ttu-id="c731f-190">команда [Curl](https://curl.haxx.se/) для тестирования API WeatherForecast;</span><span class="sxs-lookup"><span data-stu-id="c731f-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="c731f-191">URL-адрес для тестирования API WeatherForecast;</span><span class="sxs-lookup"><span data-stu-id="c731f-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="c731f-192">код, текст и заголовки ответа;</span><span class="sxs-lookup"><span data-stu-id="c731f-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="c731f-193">раскрывающийся список с типами мультимедиа и примером значения и схемы.</span><span class="sxs-lookup"><span data-stu-id="c731f-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="c731f-194">Swagger используется для создания полезной документации и страниц справки для веб-API.</span><span class="sxs-lookup"><span data-stu-id="c731f-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="c731f-195">В этом учебнике рассматривается создание веб-API.</span><span class="sxs-lookup"><span data-stu-id="c731f-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="c731f-196">Дополнительные сведения о Swagger: <xref:tutorials/web-api-help-pages-using-swagger>.</span><span class="sxs-lookup"><span data-stu-id="c731f-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="c731f-197">Скопируйте и вставьте **URL-адрес запроса** в адресную строку браузера: `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="c731f-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="c731f-198">Возвращаемые данные JSON будут выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="c731f-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="c731f-199">Обновление launchUrl</span><span class="sxs-lookup"><span data-stu-id="c731f-199">Update the launchUrl</span></span>

<span data-ttu-id="c731f-200">В файле *Properties/launchSettings.json* обновите `launchUrl` с `"swagger"` на `"api/TodoItems"`.</span><span class="sxs-lookup"><span data-stu-id="c731f-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="c731f-201">Поскольку Swagger удален, предыдущая разметка изменит URL-адрес, который запускается в методе GET контроллера, добавленного в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="c731f-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="c731f-202">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="c731f-202">Add a model class</span></span>

<span data-ttu-id="c731f-203">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c731f-204">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c731f-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c731f-206">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c731f-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="c731f-207">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="c731f-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c731f-208">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="c731f-209">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="c731f-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c731f-210">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="c731f-211">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c731f-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c731f-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c731f-213">Добавьте папку с именем *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="c731f-214">Добавьте в папку *Models* класс `TodoItem` со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c731f-215">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c731f-216">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c731f-216">Right-click the project.</span></span> <span data-ttu-id="c731f-217">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="c731f-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c731f-218">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-218">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c731f-220">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="c731f-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="c731f-221">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c731f-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="c731f-222">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="c731f-223">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c731f-224">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="c731f-225">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="c731f-225">Add a database context</span></span>

<span data-ttu-id="c731f-226">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c731f-227">Этот класс является производным от класса <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c731f-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="c731f-229">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="c731f-229">Add NuGet packages</span></span>

* <span data-ttu-id="c731f-230">В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.</span><span class="sxs-lookup"><span data-stu-id="c731f-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="c731f-231">Перейдите на вкладку **Обзор** и введите **Microsoft.EntityFrameworkCore.SqlServer** в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="c731f-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="c731f-232">На панели слева выберите **Microsoft.EntityFrameworkCore.SqlServer**.</span><span class="sxs-lookup"><span data-stu-id="c731f-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="c731f-233">Установите флажок **Проект** на правой панели и выберите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="c731f-234">Добавьте пакет NuGet **пакет NuGet Microsoft.EntityFrameworkCore.InMemory**, выполнив приведенные выше инструкции.</span><span class="sxs-lookup"><span data-stu-id="c731f-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="c731f-235">![диспетчер пакетов NuGet](first-web-api/_static/5/vsNuGet.png);</span><span class="sxs-lookup"><span data-stu-id="c731f-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="c731f-236">Добавление контекста базы данных TodoContext</span><span class="sxs-lookup"><span data-stu-id="c731f-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="c731f-237">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="c731f-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c731f-238">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c731f-239">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c731f-240">Добавьте класс `TodoContext` в папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="c731f-241">Введите следующий код:</span><span class="sxs-lookup"><span data-stu-id="c731f-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="c731f-242">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="c731f-242">Register the database context</span></span>

<span data-ttu-id="c731f-243">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c731f-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c731f-244">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="c731f-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="c731f-245">Обновите файл *Startup.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="c731f-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="c731f-246">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="c731f-246">The preceding code:</span></span>

* <span data-ttu-id="c731f-247">Удаляет вызовы Swagger.</span><span class="sxs-lookup"><span data-stu-id="c731f-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="c731f-248">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="c731f-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c731f-249">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c731f-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c731f-250">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="c731f-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="c731f-251">Формирование шаблонов контроллера</span><span class="sxs-lookup"><span data-stu-id="c731f-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c731f-253">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c731f-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c731f-254">Щелкните **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="c731f-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c731f-255">Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="c731f-256">В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="c731f-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="c731f-257">Выберите **TodoItem (TodoApi.Models)** в поле **Класс модели**.</span><span class="sxs-lookup"><span data-stu-id="c731f-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="c731f-258">Выберите **TodoContext (TodoApi.Models)** в поле **Класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="c731f-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="c731f-259">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c731f-260">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c731f-261">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c731f-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="c731f-262">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="c731f-262">The preceding commands:</span></span>

* <span data-ttu-id="c731f-263">добавляют пакеты NuGet, необходимые для формирования шаблонов;</span><span class="sxs-lookup"><span data-stu-id="c731f-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="c731f-264">устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);</span><span class="sxs-lookup"><span data-stu-id="c731f-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="c731f-265">формируют шаблоны для `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="c731f-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="c731f-266">Сформированный код:</span><span class="sxs-lookup"><span data-stu-id="c731f-266">The generated code:</span></span>

* <span data-ttu-id="c731f-267">Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="c731f-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="c731f-268">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="c731f-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c731f-269">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="c731f-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c731f-270">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="c731f-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c731f-271">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="c731f-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="c731f-272">Шаблоны ASP.NET Core для:</span><span class="sxs-lookup"><span data-stu-id="c731f-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="c731f-273">Контроллеры с представлениями включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="c731f-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="c731f-274">Контроллеры API не включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="c731f-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="c731f-275">Если токен `[action]` не находится в шаблоне маршрута, имя [действия](xref:mvc/controllers/routing#action) исключается из маршрута.</span><span class="sxs-lookup"><span data-stu-id="c731f-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="c731f-276">То есть имя связанного метода действия не используется в соответствующем маршруте.</span><span class="sxs-lookup"><span data-stu-id="c731f-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="c731f-277">Обновление метода создания PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="c731f-278">Измените инструкцию возврата в `PostTodoItem` и используйте оператор [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="c731f-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="c731f-279">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="c731f-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="c731f-280">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="c731f-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c731f-281">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c731f-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c731f-282">Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="c731f-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="c731f-283">В случае успеха возвращает [код состояния HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201).</span><span class="sxs-lookup"><span data-stu-id="c731f-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="c731f-284">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="c731f-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c731f-285">Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location).</span><span class="sxs-lookup"><span data-stu-id="c731f-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="c731f-286">Заголовок `Location` указывает [URI](https://developer.mozilla.org/docs/Glossary/URI) новой созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="c731f-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="c731f-287">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c731f-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c731f-288">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="c731f-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c731f-289">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="c731f-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="c731f-290">Установка Postman</span><span class="sxs-lookup"><span data-stu-id="c731f-290">Install Postman</span></span>

<span data-ttu-id="c731f-291">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c731f-292">Установка [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="c731f-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="c731f-293">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-293">Start the web app.</span></span>
* <span data-ttu-id="c731f-294">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-294">Start Postman.</span></span>
* <span data-ttu-id="c731f-295">Отключение параметра **Проверка SSL-сертификата**</span><span class="sxs-lookup"><span data-stu-id="c731f-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="c731f-296">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="c731f-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="c731f-297">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="c731f-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="c731f-298">Тестирование PostTodoItem с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="c731f-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="c731f-299">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="c731f-299">Create a new request.</span></span>
* <span data-ttu-id="c731f-300">Установите HTTP-метод `POST`.</span><span class="sxs-lookup"><span data-stu-id="c731f-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c731f-301">Задайте для URI значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c731f-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c731f-302">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c731f-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c731f-303">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="c731f-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="c731f-304">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="c731f-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c731f-305">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="c731f-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="c731f-306">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="c731f-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c731f-307">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-307">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="c731f-309">Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="c731f-309">Test the location header URI</span></span>

<span data-ttu-id="c731f-310">URI заголовка расположения можно протестировать в браузере.</span><span class="sxs-lookup"><span data-stu-id="c731f-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="c731f-311">Скопируйте и вставьте URI заголовка расположения в строку браузера.</span><span class="sxs-lookup"><span data-stu-id="c731f-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="c731f-312">Чтобы протестировать в Postman, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="c731f-312">To test in Postman:</span></span>

* <span data-ttu-id="c731f-313">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="c731f-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c731f-314">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="c731f-314">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="c731f-316">Установите HTTP-метод `GET`.</span><span class="sxs-lookup"><span data-stu-id="c731f-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="c731f-317">Задайте для URI значение `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="c731f-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="c731f-318">Например, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="c731f-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="c731f-319">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="c731f-320">Знакомство с методами GET</span><span class="sxs-lookup"><span data-stu-id="c731f-320">Examine the GET methods</span></span>

<span data-ttu-id="c731f-321">Реализуются две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="c731f-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="c731f-322">Протестируйте приложение, вызвав эти две конечные точки в браузере или в Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="c731f-323">Пример:</span><span class="sxs-lookup"><span data-stu-id="c731f-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="c731f-324">При вызове `GetTodoItems` возвращается примерно такой ответ:</span><span class="sxs-lookup"><span data-stu-id="c731f-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="c731f-325">Тестирование Get с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="c731f-325">Test Get with Postman</span></span>

* <span data-ttu-id="c731f-326">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="c731f-326">Create a new request.</span></span>
* <span data-ttu-id="c731f-327">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="c731f-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="c731f-328">Задайте для URI запроса значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c731f-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c731f-329">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c731f-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c731f-330">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c731f-331">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-331">Select **Send**.</span></span>

<span data-ttu-id="c731f-332">Это приложение использует выполняющуюся в памяти базу данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-332">This app uses an in-memory database.</span></span> <span data-ttu-id="c731f-333">Если остановить и вновь запустить его, предшествующий запрос GET не возвратит никаких данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="c731f-334">Если данные не возвращаются, данные для приложения получаются методом [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="c731f-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="c731f-335">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="c731f-335">Routing and URL paths</span></span>

<span data-ttu-id="c731f-336">Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="c731f-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c731f-337">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c731f-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c731f-338">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="c731f-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="c731f-339">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="c731f-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c731f-340">В этом примере класс контроллера имеет имя **TodoItems**, а сам контроллер, соответственно, — "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="c731f-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="c731f-341">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="c731f-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c731f-342">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="c731f-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c731f-343">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="c731f-343">This sample doesn't use a template.</span></span> <span data-ttu-id="c731f-344">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c731f-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c731f-345">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="c731f-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c731f-346">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="c731f-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="c731f-347">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="c731f-347">Return values</span></span>

<span data-ttu-id="c731f-348">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="c731f-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c731f-349">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="c731f-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c731f-350">Код ответа для этого типа возвращаемого значения равен [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="c731f-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c731f-351">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="c731f-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c731f-352">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="c731f-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c731f-353">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="c731f-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c731f-354">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает код ошибки [ 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.</span><span class="sxs-lookup"><span data-stu-id="c731f-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="c731f-355">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="c731f-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c731f-356">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="c731f-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="c731f-357">Метод PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-357">The PutTodoItem method</span></span>

<span data-ttu-id="c731f-358">Проверьте метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c731f-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="c731f-359">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="c731f-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c731f-360">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c731f-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c731f-361">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="c731f-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c731f-362">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="c731f-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c731f-363">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="c731f-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="c731f-364">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="c731f-365">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="c731f-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c731f-366">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="c731f-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c731f-367">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c731f-368">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя `"feed fish"`:</span><span class="sxs-lookup"><span data-stu-id="c731f-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c731f-369">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="c731f-369">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="c731f-371">Метод DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="c731f-372">Проверьте метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c731f-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="c731f-373">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="c731f-374">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="c731f-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c731f-375">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="c731f-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c731f-376">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="c731f-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="c731f-377">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="c731f-378">Предотвращение избыточной публикации</span><span class="sxs-lookup"><span data-stu-id="c731f-378">Prevent over-posting</span></span>

<span data-ttu-id="c731f-379">В настоящее время пример приложения предоставляет весь объект `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c731f-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="c731f-380">Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели.</span><span class="sxs-lookup"><span data-stu-id="c731f-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="c731f-381">Это связано с несколькими причинами, и безопасность является основной.</span><span class="sxs-lookup"><span data-stu-id="c731f-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="c731f-382">Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления.</span><span class="sxs-lookup"><span data-stu-id="c731f-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="c731f-383">В этой статье используется **DTO**.</span><span class="sxs-lookup"><span data-stu-id="c731f-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="c731f-384">DTO можно использовать для следующего:</span><span class="sxs-lookup"><span data-stu-id="c731f-384">A DTO may be used to:</span></span>

* <span data-ttu-id="c731f-385">Предотвращение избыточной публикации.</span><span class="sxs-lookup"><span data-stu-id="c731f-385">Prevent over-posting.</span></span>
* <span data-ttu-id="c731f-386">Скрытие свойств, которые не предназначены для просмотра клиентами.</span><span class="sxs-lookup"><span data-stu-id="c731f-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="c731f-387">Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="c731f-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="c731f-388">Сведение графов объектов, содержащих вложенные объекты.</span><span class="sxs-lookup"><span data-stu-id="c731f-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="c731f-389">Сведенные графы объектов могут быть удобнее для клиентов.</span><span class="sxs-lookup"><span data-stu-id="c731f-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="c731f-390">Чтобы продемонстрировать подход с применением DTO, обновите класс `TodoItem`, включив в него поле секрета:</span><span class="sxs-lookup"><span data-stu-id="c731f-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="c731f-391">Поле секрета должно быть скрыто в этом приложении, однако административное приложение может отобразить его.</span><span class="sxs-lookup"><span data-stu-id="c731f-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="c731f-392">Убедитесь, что вы можете отправить и получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="c731f-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="c731f-393">Создайте модель DTO:</span><span class="sxs-lookup"><span data-stu-id="c731f-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="c731f-394">Обновите `TodoItemsController` для использования `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="c731f-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="c731f-395">Убедитесь, что вы можете отправить или получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="c731f-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="c731f-396">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="c731f-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="c731f-397">См. руководство по [: Вызовите веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="c731f-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="c731f-398">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="c731f-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c731f-399">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="c731f-399">Create a web API project.</span></span>
> * <span data-ttu-id="c731f-400">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c731f-401">Формирование шаблонов контроллера с использованием методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="c731f-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="c731f-402">Настройка маршрутизации, URL-пути и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="c731f-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="c731f-403">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-403">Call the web API with Postman.</span></span>

<span data-ttu-id="c731f-404">В итоге вы получите веб-API, позволяющий работать с элементами списка дел, хранимыми в базе данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="c731f-405">Обзор</span><span class="sxs-lookup"><span data-stu-id="c731f-405">Overview</span></span>

<span data-ttu-id="c731f-406">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="c731f-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c731f-407">API</span><span class="sxs-lookup"><span data-stu-id="c731f-407">API</span></span> | <span data-ttu-id="c731f-408">Описание</span><span class="sxs-lookup"><span data-stu-id="c731f-408">Description</span></span> | <span data-ttu-id="c731f-409">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="c731f-409">Request body</span></span> | <span data-ttu-id="c731f-410">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="c731f-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="c731f-411">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="c731f-411">Get all to-do items</span></span> | <span data-ttu-id="c731f-412">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-412">None</span></span> | <span data-ttu-id="c731f-413">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="c731f-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="c731f-414">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="c731f-414">Get an item by ID</span></span> | <span data-ttu-id="c731f-415">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-415">None</span></span> | <span data-ttu-id="c731f-416">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="c731f-417">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="c731f-417">Add a new item</span></span> | <span data-ttu-id="c731f-418">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-418">To-do item</span></span> | <span data-ttu-id="c731f-419">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="c731f-420">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c731f-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c731f-421">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-421">To-do item</span></span> | <span data-ttu-id="c731f-422">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-422">None</span></span> |
|<span data-ttu-id="c731f-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c731f-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="c731f-424">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c731f-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c731f-425">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-425">None</span></span> | <span data-ttu-id="c731f-426">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-426">None</span></span>|

<span data-ttu-id="c731f-427">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="c731f-427">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="c731f-433">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="c731f-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c731f-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c731f-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c731f-436">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="c731f-437">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="c731f-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c731f-439">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="c731f-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c731f-440">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c731f-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="c731f-441">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c731f-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="c731f-442">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="c731f-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="c731f-443">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c731f-443">Select the **API** template and click **Create**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c731f-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c731f-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c731f-446">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c731f-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c731f-447">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="c731f-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c731f-448">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c731f-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="c731f-449">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c731f-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="c731f-450">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="c731f-450">The preceding commands:</span></span>

  * <span data-ttu-id="c731f-451">Создает новый проект веб-API и открывает его в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c731f-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="c731f-452">Добавляет пакеты NuGet, которые понадобятся в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="c731f-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c731f-453">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c731f-454">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="c731f-454">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c731f-456">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c731f-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="c731f-457">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c731f-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Выбор шаблона API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="c731f-459">В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **целевой платформы** выберите последнюю версию .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="c731f-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="c731f-460">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c731f-460">Select **Next**.</span></span>

* <span data-ttu-id="c731f-461">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c731f-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="c731f-463">Откройте командный терминал в папке проекта и выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c731f-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="c731f-464">Тестирование API</span><span class="sxs-lookup"><span data-stu-id="c731f-464">Test the API</span></span>

<span data-ttu-id="c731f-465">Шаблон проекта создает API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="c731f-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="c731f-466">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="c731f-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c731f-468">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c731f-469">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/WeatherForecast`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="c731f-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="c731f-470">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c731f-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="c731f-471">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c731f-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c731f-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c731f-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c731f-473">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c731f-474">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="c731f-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c731f-475">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c731f-476">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c731f-477">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="c731f-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c731f-478">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="c731f-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c731f-479">Добавьте `/WeatherForecast` к URL-адресу (измените URL-адрес на `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="c731f-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="c731f-480">Возвращаемые данные JSON будут выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="c731f-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="c731f-481">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="c731f-481">Add a model class</span></span>

<span data-ttu-id="c731f-482">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c731f-483">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c731f-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c731f-485">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c731f-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="c731f-486">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="c731f-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c731f-487">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="c731f-488">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="c731f-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c731f-489">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="c731f-490">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c731f-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c731f-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c731f-492">Добавьте папку с именем *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="c731f-493">Добавьте в папку *Models* класс `TodoItem` со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c731f-494">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c731f-495">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c731f-495">Right-click the project.</span></span> <span data-ttu-id="c731f-496">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="c731f-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c731f-497">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-497">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c731f-499">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="c731f-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="c731f-500">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c731f-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="c731f-501">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="c731f-502">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c731f-503">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="c731f-504">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="c731f-504">Add a database context</span></span>

<span data-ttu-id="c731f-505">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c731f-506">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="c731f-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="c731f-508">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="c731f-508">Add NuGet packages</span></span>

* <span data-ttu-id="c731f-509">В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.</span><span class="sxs-lookup"><span data-stu-id="c731f-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="c731f-510">Перейдите на вкладку **Обзор** и введите **Microsoft.EntityFrameworkCore.SqlServer** в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="c731f-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="c731f-511">На панели слева выберите **Microsoft.EntityFrameworkCore.SqlServer**.</span><span class="sxs-lookup"><span data-stu-id="c731f-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="c731f-512">Установите флажок **Проект** на правой панели и выберите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="c731f-513">Добавьте пакет NuGet **пакет NuGet Microsoft.EntityFrameworkCore.InMemory**, выполнив приведенные выше инструкции.</span><span class="sxs-lookup"><span data-stu-id="c731f-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Диспетчер пакетов NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="c731f-515">Добавление контекста базы данных TodoContext</span><span class="sxs-lookup"><span data-stu-id="c731f-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="c731f-516">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="c731f-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c731f-517">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c731f-518">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c731f-519">Добавьте класс `TodoContext` в папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="c731f-520">Введите следующий код:</span><span class="sxs-lookup"><span data-stu-id="c731f-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="c731f-521">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="c731f-521">Register the database context</span></span>

<span data-ttu-id="c731f-522">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c731f-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c731f-523">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="c731f-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="c731f-524">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="c731f-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="c731f-525">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="c731f-525">The preceding code:</span></span>

* <span data-ttu-id="c731f-526">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="c731f-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c731f-527">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c731f-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c731f-528">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="c731f-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="c731f-529">Формирование шаблонов контроллера</span><span class="sxs-lookup"><span data-stu-id="c731f-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-530">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c731f-531">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c731f-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c731f-532">Щелкните **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="c731f-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c731f-533">Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="c731f-534">В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="c731f-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="c731f-535">Выберите **TodoItem (TodoApi.Models)** в поле **Класс модели**.</span><span class="sxs-lookup"><span data-stu-id="c731f-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="c731f-536">Выберите **TodoContext (TodoApi.Models)** в поле **Класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="c731f-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="c731f-537">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c731f-538">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c731f-539">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c731f-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="c731f-540">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="c731f-540">The preceding commands:</span></span>

* <span data-ttu-id="c731f-541">добавляют пакеты NuGet, необходимые для формирования шаблонов;</span><span class="sxs-lookup"><span data-stu-id="c731f-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="c731f-542">устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);</span><span class="sxs-lookup"><span data-stu-id="c731f-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="c731f-543">формируют шаблоны для `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="c731f-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="c731f-544">Сформированный код:</span><span class="sxs-lookup"><span data-stu-id="c731f-544">The generated code:</span></span>

* <span data-ttu-id="c731f-545">Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="c731f-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="c731f-546">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="c731f-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c731f-547">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="c731f-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c731f-548">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="c731f-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c731f-549">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="c731f-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="c731f-550">Шаблоны ASP.NET Core для:</span><span class="sxs-lookup"><span data-stu-id="c731f-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="c731f-551">Контроллеры с представлениями включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="c731f-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="c731f-552">Контроллеры API не включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="c731f-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="c731f-553">Если токен `[action]` не находится в шаблоне маршрута, имя [действия](xref:mvc/controllers/routing#action) исключается из маршрута.</span><span class="sxs-lookup"><span data-stu-id="c731f-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="c731f-554">То есть имя связанного метода действия не используется в соответствующем маршруте.</span><span class="sxs-lookup"><span data-stu-id="c731f-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="c731f-555">Знакомство с методом создания PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="c731f-556">Измените инструкцию возврата в `PostTodoItem` и используйте оператор [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="c731f-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="c731f-557">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="c731f-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="c731f-558">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="c731f-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c731f-559">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c731f-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c731f-560">Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="c731f-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="c731f-561">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="c731f-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="c731f-562">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="c731f-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c731f-563">Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location).</span><span class="sxs-lookup"><span data-stu-id="c731f-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="c731f-564">Заголовок `Location` указывает [URI](https://developer.mozilla.org/docs/Glossary/URI) новой созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="c731f-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="c731f-565">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c731f-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c731f-566">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="c731f-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c731f-567">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="c731f-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="c731f-568">Установка Postman</span><span class="sxs-lookup"><span data-stu-id="c731f-568">Install Postman</span></span>

<span data-ttu-id="c731f-569">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c731f-570">Установка [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="c731f-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="c731f-571">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-571">Start the web app.</span></span>
* <span data-ttu-id="c731f-572">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-572">Start Postman.</span></span>
* <span data-ttu-id="c731f-573">Отключение параметра **Проверка SSL-сертификата**</span><span class="sxs-lookup"><span data-stu-id="c731f-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="c731f-574">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="c731f-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="c731f-575">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="c731f-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="c731f-576">Тестирование PostTodoItem с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="c731f-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="c731f-577">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="c731f-577">Create a new request.</span></span>
* <span data-ttu-id="c731f-578">Установите HTTP-метод `POST`.</span><span class="sxs-lookup"><span data-stu-id="c731f-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c731f-579">Задайте для URI значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c731f-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c731f-580">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c731f-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c731f-581">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="c731f-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="c731f-582">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="c731f-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c731f-583">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="c731f-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="c731f-584">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="c731f-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c731f-585">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-585">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="c731f-587">Тестирование URI заголовка расположения с помощью Postman</span><span class="sxs-lookup"><span data-stu-id="c731f-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="c731f-588">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="c731f-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c731f-589">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="c731f-589">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="c731f-591">Установите HTTP-метод `GET`.</span><span class="sxs-lookup"><span data-stu-id="c731f-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="c731f-592">Задайте для URI значение `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="c731f-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="c731f-593">Например, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="c731f-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="c731f-594">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="c731f-595">Знакомство с методами GET</span><span class="sxs-lookup"><span data-stu-id="c731f-595">Examine the GET methods</span></span>

<span data-ttu-id="c731f-596">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="c731f-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="c731f-597">Протестируйте приложение, вызвав эти две конечные точки в браузере или в Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="c731f-598">Пример:</span><span class="sxs-lookup"><span data-stu-id="c731f-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="c731f-599">При вызове `GetTodoItems` возвращается примерно такой ответ:</span><span class="sxs-lookup"><span data-stu-id="c731f-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="c731f-600">Тестирование Get с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="c731f-600">Test Get with Postman</span></span>

* <span data-ttu-id="c731f-601">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="c731f-601">Create a new request.</span></span>
* <span data-ttu-id="c731f-602">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="c731f-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="c731f-603">Задайте для URI запроса значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c731f-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c731f-604">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c731f-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c731f-605">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c731f-606">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-606">Select **Send**.</span></span>

<span data-ttu-id="c731f-607">Это приложение использует выполняющуюся в памяти базу данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-607">This app uses an in-memory database.</span></span> <span data-ttu-id="c731f-608">Если остановить и вновь запустить его, предшествующий запрос GET не возвратит никаких данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="c731f-609">Если данные не возвращаются, данные для приложения получаются методом [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="c731f-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="c731f-610">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="c731f-610">Routing and URL paths</span></span>

<span data-ttu-id="c731f-611">Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="c731f-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c731f-612">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c731f-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c731f-613">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="c731f-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="c731f-614">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="c731f-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c731f-615">В этом примере класс контроллера имеет имя **TodoItems**, а сам контроллер, соответственно, — "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="c731f-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="c731f-616">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="c731f-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c731f-617">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="c731f-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c731f-618">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="c731f-618">This sample doesn't use a template.</span></span> <span data-ttu-id="c731f-619">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c731f-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c731f-620">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="c731f-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c731f-621">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="c731f-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="c731f-622">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="c731f-622">Return values</span></span> 

<span data-ttu-id="c731f-623">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="c731f-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c731f-624">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="c731f-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c731f-625">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="c731f-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c731f-626">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="c731f-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c731f-627">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="c731f-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c731f-628">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="c731f-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c731f-629">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>).</span><span class="sxs-lookup"><span data-stu-id="c731f-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="c731f-630">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="c731f-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c731f-631">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="c731f-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="c731f-632">Метод PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-632">The PutTodoItem method</span></span>

<span data-ttu-id="c731f-633">Проверьте метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c731f-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="c731f-634">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="c731f-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c731f-635">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c731f-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c731f-636">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="c731f-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c731f-637">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="c731f-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c731f-638">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="c731f-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="c731f-639">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="c731f-640">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="c731f-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c731f-641">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="c731f-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c731f-642">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c731f-643">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="c731f-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c731f-644">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="c731f-644">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="c731f-646">Метод DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="c731f-647">Проверьте метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c731f-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="c731f-648">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="c731f-649">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="c731f-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c731f-650">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="c731f-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c731f-651">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="c731f-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="c731f-652">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="c731f-653">Предотвращение избыточной публикации</span><span class="sxs-lookup"><span data-stu-id="c731f-653">Prevent over-posting</span></span>

<span data-ttu-id="c731f-654">В настоящее время пример приложения предоставляет весь объект `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c731f-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="c731f-655">Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели.</span><span class="sxs-lookup"><span data-stu-id="c731f-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="c731f-656">Это связано с несколькими причинами, и безопасность является основной.</span><span class="sxs-lookup"><span data-stu-id="c731f-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="c731f-657">Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления.</span><span class="sxs-lookup"><span data-stu-id="c731f-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="c731f-658">В этой статье используется **DTO**.</span><span class="sxs-lookup"><span data-stu-id="c731f-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="c731f-659">DTO можно использовать для следующего:</span><span class="sxs-lookup"><span data-stu-id="c731f-659">A DTO may be used to:</span></span>

* <span data-ttu-id="c731f-660">Предотвращение избыточной публикации.</span><span class="sxs-lookup"><span data-stu-id="c731f-660">Prevent over-posting.</span></span>
* <span data-ttu-id="c731f-661">Скрытие свойств, которые не предназначены для просмотра клиентами.</span><span class="sxs-lookup"><span data-stu-id="c731f-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="c731f-662">Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="c731f-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="c731f-663">Сведение графов объектов, содержащих вложенные объекты.</span><span class="sxs-lookup"><span data-stu-id="c731f-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="c731f-664">Сведенные графы объектов могут быть удобнее для клиентов.</span><span class="sxs-lookup"><span data-stu-id="c731f-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="c731f-665">Чтобы продемонстрировать подход с применением DTO, обновите класс `TodoItem`, включив в него поле секрета:</span><span class="sxs-lookup"><span data-stu-id="c731f-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="c731f-666">Поле секрета должно быть скрыто в этом приложении, однако административное приложение может отобразить его.</span><span class="sxs-lookup"><span data-stu-id="c731f-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="c731f-667">Убедитесь, что вы можете отправить и получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="c731f-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="c731f-668">Создайте модель DTO:</span><span class="sxs-lookup"><span data-stu-id="c731f-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="c731f-669">Обновите `TodoItemsController` для использования `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="c731f-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="c731f-670">Убедитесь, что вы можете отправить или получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="c731f-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="c731f-671">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="c731f-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="c731f-672">См. руководство по [: Вызовите веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="c731f-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c731f-673">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="c731f-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c731f-674">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="c731f-674">Create a web API project.</span></span>
> * <span data-ttu-id="c731f-675">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c731f-676">Добавление контроллера.</span><span class="sxs-lookup"><span data-stu-id="c731f-676">Add a controller.</span></span>
> * <span data-ttu-id="c731f-677">Добавление методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="c731f-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="c731f-678">Настройка маршрутизации и путей URL.</span><span class="sxs-lookup"><span data-stu-id="c731f-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="c731f-679">Указание возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="c731f-679">Specify return values.</span></span>
> * <span data-ttu-id="c731f-680">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="c731f-681">Вызовите веб-API с помощью JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c731f-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="c731f-682">В конечном итоге вы получите веб-API, обеспечивающий управление элементами списка дел, хранящимися в реляционной базе данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="c731f-683">2\.1. Обзор</span><span class="sxs-lookup"><span data-stu-id="c731f-683">Overview 2.1</span></span>

<span data-ttu-id="c731f-684">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="c731f-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c731f-685">API</span><span class="sxs-lookup"><span data-stu-id="c731f-685">API</span></span> | <span data-ttu-id="c731f-686">Описание</span><span class="sxs-lookup"><span data-stu-id="c731f-686">Description</span></span> | <span data-ttu-id="c731f-687">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="c731f-687">Request body</span></span> | <span data-ttu-id="c731f-688">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="c731f-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="c731f-689">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="c731f-689">GET /api/TodoItems</span></span> | <span data-ttu-id="c731f-690">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="c731f-690">Get all to-do items</span></span> | <span data-ttu-id="c731f-691">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-691">None</span></span> | <span data-ttu-id="c731f-692">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="c731f-692">Array of to-do items</span></span>|
|<span data-ttu-id="c731f-693">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="c731f-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="c731f-694">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="c731f-694">Get an item by ID</span></span> | <span data-ttu-id="c731f-695">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-695">None</span></span> | <span data-ttu-id="c731f-696">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-696">To-do item</span></span>|
|<span data-ttu-id="c731f-697">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="c731f-697">POST /api/TodoItems</span></span> | <span data-ttu-id="c731f-698">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="c731f-698">Add a new item</span></span> | <span data-ttu-id="c731f-699">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-699">To-do item</span></span> | <span data-ttu-id="c731f-700">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-700">To-do item</span></span> |
|<span data-ttu-id="c731f-701">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="c731f-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="c731f-702">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c731f-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c731f-703">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-703">To-do item</span></span> | <span data-ttu-id="c731f-704">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-704">None</span></span> |
|<span data-ttu-id="c731f-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c731f-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="c731f-706">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c731f-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c731f-707">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-707">None</span></span> | <span data-ttu-id="c731f-708">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="c731f-708">None</span></span>|

<span data-ttu-id="c731f-709">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="c731f-709">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="c731f-715">2\.1. Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="c731f-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-716">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c731f-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c731f-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c731f-718">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="c731f-719">2\.1. Создание веб-проекта</span><span class="sxs-lookup"><span data-stu-id="c731f-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-720">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c731f-721">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="c731f-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c731f-722">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c731f-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="c731f-723">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c731f-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="c731f-724">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="c731f-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="c731f-725">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c731f-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="c731f-726">**Не** выбирайте **Включение поддержки Docker**.</span><span class="sxs-lookup"><span data-stu-id="c731f-726">**Don't** select **Enable Docker Support**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c731f-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c731f-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c731f-729">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c731f-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c731f-730">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="c731f-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c731f-731">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c731f-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="c731f-732">С помощью этих команд создается новый проект веб-API и открывается новый экземпляр Visual Studio Code в новой папке проекта.</span><span class="sxs-lookup"><span data-stu-id="c731f-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="c731f-733">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c731f-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c731f-734">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c731f-735">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="c731f-735">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c731f-737">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c731f-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="c731f-738">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c731f-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="c731f-739">В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **целевой платформы** выберите последнюю версию .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="c731f-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="c731f-740">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="c731f-740">Select **Next**.</span></span>

* <span data-ttu-id="c731f-741">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c731f-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="c731f-743">2\.1. Тестирование API</span><span class="sxs-lookup"><span data-stu-id="c731f-743">Test the API 2.1</span></span>

<span data-ttu-id="c731f-744">Шаблон проекта создает API `values`.</span><span class="sxs-lookup"><span data-stu-id="c731f-744">The project template creates a `values` API.</span></span> <span data-ttu-id="c731f-745">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="c731f-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-746">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c731f-747">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c731f-748">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/api/values`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="c731f-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="c731f-749">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c731f-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="c731f-750">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="c731f-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c731f-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c731f-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c731f-752">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c731f-753">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="c731f-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c731f-754">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c731f-755">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c731f-756">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="c731f-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c731f-757">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="c731f-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c731f-758">Добавьте `/api/values` к URL-адресу (измените URL-адрес на `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="c731f-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="c731f-759">Возвращается следующий файл JSON:</span><span class="sxs-lookup"><span data-stu-id="c731f-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="c731f-760">2\.1. Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="c731f-760">Add a model class 2.1</span></span>

<span data-ttu-id="c731f-761">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c731f-762">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c731f-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-763">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c731f-764">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c731f-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="c731f-765">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="c731f-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c731f-766">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="c731f-767">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="c731f-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c731f-768">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="c731f-769">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c731f-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c731f-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c731f-771">Добавьте папку с именем *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="c731f-772">Добавьте в папку *Models* класс `TodoItem` со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c731f-773">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c731f-774">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c731f-774">Right-click the project.</span></span> <span data-ttu-id="c731f-775">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="c731f-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c731f-776">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-776">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c731f-778">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="c731f-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="c731f-779">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="c731f-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="c731f-780">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="c731f-781">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c731f-782">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="c731f-783">2\.1. Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="c731f-783">Add a database context 2.1</span></span>

<span data-ttu-id="c731f-784">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c731f-785">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="c731f-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-786">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c731f-787">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="c731f-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c731f-788">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c731f-789">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c731f-790">Добавьте класс `TodoContext` в папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="c731f-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="c731f-791">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="c731f-792">2\.1. Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="c731f-792">Register the database context 2.1</span></span>

<span data-ttu-id="c731f-793">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c731f-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c731f-794">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="c731f-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="c731f-795">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="c731f-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="c731f-796">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="c731f-796">The preceding code:</span></span>

* <span data-ttu-id="c731f-797">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="c731f-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c731f-798">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c731f-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c731f-799">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="c731f-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="c731f-800">2\.1. Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="c731f-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-801">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c731f-802">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="c731f-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c731f-803">Щелкните **Добавить** > **Создать элемент**.</span><span class="sxs-lookup"><span data-stu-id="c731f-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="c731f-804">В диалоговом окне **Добавить новый элемент** выберите шаблон **Класс контроллера API**.</span><span class="sxs-lookup"><span data-stu-id="c731f-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="c731f-805">Присвойте классу имя *TodoController* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![Диалоговое окно добавления элемента с контроллером в поле поиска и выбранным контроллером веб-API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c731f-807">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c731f-808">В папке *Controllers* создайте класс с именем `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="c731f-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="c731f-809">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="c731f-810">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="c731f-810">The preceding code:</span></span>

* <span data-ttu-id="c731f-811">Определяет класс контроллера API без методов.</span><span class="sxs-lookup"><span data-stu-id="c731f-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="c731f-812">Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="c731f-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="c731f-813">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="c731f-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c731f-814">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="c731f-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c731f-815">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="c731f-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c731f-816">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="c731f-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="c731f-817">Добавляет элемент `Item1` в базу данных, если она пуста.</span><span class="sxs-lookup"><span data-stu-id="c731f-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="c731f-818">Этот код находится в конструкторе и выполняется каждый раз при обнаружении нового HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="c731f-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="c731f-819">Если вы удалите все элементы, конструктор создаст `Item1` при следующем вызове метода API.</span><span class="sxs-lookup"><span data-stu-id="c731f-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="c731f-820">Поэтому может создаться впечатление, что удаление не было выполнено, хотя это не так.</span><span class="sxs-lookup"><span data-stu-id="c731f-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="c731f-821">2\.1. Добавление методов Get</span><span class="sxs-lookup"><span data-stu-id="c731f-821">Add Get methods 2.1</span></span>

<span data-ttu-id="c731f-822">Чтобы получить API, который извлекает элементы списка дел, добавьте следующие методы в класс `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="c731f-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="c731f-823">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="c731f-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="c731f-824">Если приложение все еще выполняется, оно останавливается.</span><span class="sxs-lookup"><span data-stu-id="c731f-824">Stop the app if it's still running.</span></span> <span data-ttu-id="c731f-825">Затем оно запускается снова с последними изменениями.</span><span class="sxs-lookup"><span data-stu-id="c731f-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="c731f-826">Протестируйте приложение, вызвав эти две конечные точки в браузере.</span><span class="sxs-lookup"><span data-stu-id="c731f-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="c731f-827">Пример:</span><span class="sxs-lookup"><span data-stu-id="c731f-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="c731f-828">При вызове `GetTodoItems` возвращается следующий ответ HTTP:</span><span class="sxs-lookup"><span data-stu-id="c731f-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="c731f-829">2\.1. Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="c731f-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="c731f-830">Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="c731f-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c731f-831">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c731f-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c731f-832">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="c731f-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="c731f-833">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="c731f-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c731f-834">В этом примере класс контроллера носит имя **Todo**, а сам контроллер, соответственно, — "todo".</span><span class="sxs-lookup"><span data-stu-id="c731f-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="c731f-835">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="c731f-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c731f-836">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="c731f-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c731f-837">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="c731f-837">This sample doesn't use a template.</span></span> <span data-ttu-id="c731f-838">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c731f-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c731f-839">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="c731f-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c731f-840">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="c731f-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="c731f-841">2\.1. Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="c731f-841">Return values 2.1</span></span>

<span data-ttu-id="c731f-842">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="c731f-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c731f-843">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="c731f-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c731f-844">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="c731f-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c731f-845">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="c731f-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c731f-846">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="c731f-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c731f-847">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="c731f-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c731f-848">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>).</span><span class="sxs-lookup"><span data-stu-id="c731f-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="c731f-849">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="c731f-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c731f-850">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="c731f-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="c731f-851">2\.1. Тестирование метода GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="c731f-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="c731f-852">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c731f-853">Установите [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="c731f-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="c731f-854">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="c731f-854">Start the web app.</span></span>
* <span data-ttu-id="c731f-855">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-855">Start Postman.</span></span>
* <span data-ttu-id="c731f-856">Отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="c731f-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c731f-857">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c731f-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c731f-858">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="c731f-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c731f-859">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c731f-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c731f-860">В меню **Postman** > **Настройки** (вкладка **Общие**) отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="c731f-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="c731f-861">Кроме того, можно выбрать значок гаечного ключа и выбрать **Параметры**, а затем отключить проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="c731f-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="c731f-862">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="c731f-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="c731f-863">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="c731f-863">Create a new request.</span></span>
  * <span data-ttu-id="c731f-864">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="c731f-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="c731f-865">Задайте для URI запроса значение `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="c731f-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="c731f-866">Например, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="c731f-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="c731f-867">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="c731f-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c731f-868">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-868">Select **Send**.</span></span>

![Postman с запросом Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="c731f-870">2\.1. Добавление метода Create</span><span class="sxs-lookup"><span data-stu-id="c731f-870">Add a Create method 2.1</span></span>

<span data-ttu-id="c731f-871">Добавьте следующий метод `PostTodoItem` в *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="c731f-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="c731f-872">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="c731f-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="c731f-873">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="c731f-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c731f-874">Метод `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="c731f-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="c731f-875">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="c731f-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="c731f-876">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="c731f-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c731f-877">Добавляет заголовок `Location` в ответ.</span><span class="sxs-lookup"><span data-stu-id="c731f-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="c731f-878">Заголовок `Location` расположения указывает URI вновь созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="c731f-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="c731f-879">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c731f-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c731f-880">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="c731f-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c731f-881">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="c731f-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="c731f-882">2\.1. Тестирование метода PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="c731f-883">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="c731f-883">Build the project.</span></span>
* <span data-ttu-id="c731f-884">В Postman укажите метод HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="c731f-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c731f-885">Задайте для URI значение `https://localhost:<port>/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="c731f-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="c731f-886">Например, `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="c731f-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="c731f-887">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="c731f-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="c731f-888">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="c731f-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c731f-889">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="c731f-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="c731f-890">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="c731f-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c731f-891">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-891">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/create.png)

  <span data-ttu-id="c731f-893">Если вы получаете ошибку 405 "Недопустимый метод", это может свидетельствовать о том, что после добавления метода `PostTodoItem` не была выполнена компиляция проекта.</span><span class="sxs-lookup"><span data-stu-id="c731f-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="c731f-894">2\.1. Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="c731f-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="c731f-895">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="c731f-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c731f-896">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="c731f-896">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="c731f-898">Укажите метод GET.</span><span class="sxs-lookup"><span data-stu-id="c731f-898">Set the method to GET.</span></span>
* <span data-ttu-id="c731f-899">Задайте для URI значение `https://localhost:<port>/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="c731f-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="c731f-900">Например, `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="c731f-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="c731f-901">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="c731f-902">2\.1. Добавление метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="c731f-903">Добавьте приведенный ниже метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c731f-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="c731f-904">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="c731f-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c731f-905">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c731f-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c731f-906">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="c731f-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c731f-907">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="c731f-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c731f-908">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="c731f-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="c731f-909">2\.1. Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="c731f-910">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="c731f-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c731f-911">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="c731f-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c731f-912">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="c731f-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c731f-913">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="c731f-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c731f-914">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="c731f-914">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="c731f-916">2\.1. Добавление метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="c731f-917">Добавьте приведенный ниже метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c731f-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="c731f-918">`DeleteTodoItem`Ответ[ — 204 (нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c731f-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="c731f-919">2\.1. Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="c731f-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="c731f-920">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="c731f-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c731f-921">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="c731f-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c731f-922">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="c731f-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="c731f-923">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="c731f-923">Select **Send**.</span></span>

<span data-ttu-id="c731f-924">В этом примере приложения вы можете удалить все элементы.</span><span class="sxs-lookup"><span data-stu-id="c731f-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="c731f-925">Однако в случае удаления последнего элемента в момент следующего вызова API конструктор класса модели создаст новый элемент.</span><span class="sxs-lookup"><span data-stu-id="c731f-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="c731f-926">2\.1. Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="c731f-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="c731f-927">В этом разделе описано, как добавить HTML-страницу, которая использует JavaScript для вызова веб-API.</span><span class="sxs-lookup"><span data-stu-id="c731f-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="c731f-928">jQuery инициирует запрос.</span><span class="sxs-lookup"><span data-stu-id="c731f-928">jQuery initiates the request.</span></span> <span data-ttu-id="c731f-929">JavaScript изменяет страницу, используя сведения из ответа API.</span><span class="sxs-lookup"><span data-stu-id="c731f-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="c731f-930">Настройте приложение для [обслуживания статических файлов](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) и [включения сопоставления файлов по умолчанию](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A), обновив *Startup.cs* следующим выделенным кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="c731f-931">Создайте папку *wwwroot* в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="c731f-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="c731f-932">Добавьте HTML-файл *index.html* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="c731f-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="c731f-933">Замените его содержимое следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="c731f-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="c731f-934">Добавьте файл JavaScript с именем *site.js* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="c731f-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="c731f-935">Замените его содержимое следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="c731f-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="c731f-936">Может потребоваться изменение параметров запуска проекта ASP.NET Core для локального тестирования HTML-страницы:</span><span class="sxs-lookup"><span data-stu-id="c731f-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="c731f-937">Откройте файл *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c731f-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="c731f-938">Удалите свойство `launchUrl`, чтобы приложение открылось через *index.html* &mdash; файл проекта по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c731f-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="c731f-939">В этом примере вызываются все методы CRUD в веб-API.</span><span class="sxs-lookup"><span data-stu-id="c731f-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="c731f-940">Ниже приводится пояснение вызовов API.</span><span class="sxs-lookup"><span data-stu-id="c731f-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="c731f-941">2\.1. Получение списка элементов задач</span><span class="sxs-lookup"><span data-stu-id="c731f-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="c731f-942">jQuery отправляет запрос HTTP GET к веб-API, который возвращает ответ JSON, представляющий массив элементов списка дел.</span><span class="sxs-lookup"><span data-stu-id="c731f-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="c731f-943">В случае успешного запроса используется функция обратного вызова `success`.</span><span class="sxs-lookup"><span data-stu-id="c731f-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="c731f-944">При обратном вызове в модель DOM вносятся данные о задачах.</span><span class="sxs-lookup"><span data-stu-id="c731f-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="c731f-945">2\.1. Добавление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="c731f-946">jQuery отправляет запрос HTTP POST с элементом списка дел в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="c731f-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="c731f-947">Для параметров `accepts` и `contentType` указывается `application/json`, чтобы указать тип носителя при получении и отправке.</span><span class="sxs-lookup"><span data-stu-id="c731f-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="c731f-948">Элемент списка дел преобразуется в JSON с помощью [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="c731f-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="c731f-949">Если интерфейс API возвращает код состояния успешного выполнения, вызывается функция `getData` для обновления HTML-таблицы.</span><span class="sxs-lookup"><span data-stu-id="c731f-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="c731f-950">2\.1. Обновление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="c731f-951">Обновление элемента списка дел выполняется аналогично его добавлению.</span><span class="sxs-lookup"><span data-stu-id="c731f-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="c731f-952">`url` изменяется, чтобы добавить уникальный идентификатор для элемента, а в качестве `type` устанавливается `PUT`.</span><span class="sxs-lookup"><span data-stu-id="c731f-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="c731f-953">2\.1. Удаление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="c731f-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="c731f-954">Чтобы удалить элемент списка дел, установите для `type` в вызове AJAX значение `DELETE` и укажите уникальный идентификатор элемента в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="c731f-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="c731f-955">2\.1. Добавление поддержки аутентификации в веб-API</span><span class="sxs-lookup"><span data-stu-id="c731f-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="c731f-956">2\.1. Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c731f-956">Additional resources 2.1</span></span>

<span data-ttu-id="c731f-957">[Просмотреть или скачать пример кода для этого учебника](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="c731f-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="c731f-958">См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="c731f-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="c731f-959">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="c731f-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="c731f-960">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="c731f-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
