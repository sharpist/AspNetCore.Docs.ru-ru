---
title: Учебник. Вызов веб-API ASP.NET Core с помощью JavaScript
author: rick-anderson
description: Узнайте, как вызывать веб-API ASP.NET Core с помощью JavaScript.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: c32c5befe0be3b1ad4bd87649d3cc74b0296a134
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703713"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="08857-103">Учебник. Вызов веб-API ASP.NET Core с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="08857-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="08857-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="08857-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="08857-105">В этом руководстве описано, как вызвать веб-API ASP.NET Core с помощью JavaScript и [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span><span class="sxs-lookup"><span data-stu-id="08857-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08857-106">Если вы используете ASP.NET Core 2.2, см. соответствующий раздел руководства по [вызову веб-API с помощью JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span><span class="sxs-lookup"><span data-stu-id="08857-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="08857-107">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="08857-107">Prerequisites</span></span>

* <span data-ttu-id="08857-108">Изучите [Учебник. Создание веб-API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="08857-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="08857-109">Опыт работы с CSS, HTML и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="08857-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="08857-110">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="08857-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="08857-111">В этом разделе описано, как добавить HTML-страницу, содержащую формы для создания и администрирования элементов списка задач.</span><span class="sxs-lookup"><span data-stu-id="08857-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="08857-112">Обработчики событий присоединяются к элементам на странице.</span><span class="sxs-lookup"><span data-stu-id="08857-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="08857-113">При использовании обработчиков событий создаются запросы HTTP к методам действия веб-API.</span><span class="sxs-lookup"><span data-stu-id="08857-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="08857-114">Функция Fetch API `fetch` инициирует каждый такой запрос HTTP.</span><span class="sxs-lookup"><span data-stu-id="08857-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="08857-115">Функция `fetch` возвращает объект [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise), который содержит ответ HTTP, представленный в виде объекта `Response`.</span><span class="sxs-lookup"><span data-stu-id="08857-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="08857-116">Распространенным подходом является извлечение текста ответа JSON путем вызова функции `json` в объекте `Response`.</span><span class="sxs-lookup"><span data-stu-id="08857-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="08857-117">JavaScript изменяет страницу, используя сведения из ответа API.</span><span class="sxs-lookup"><span data-stu-id="08857-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="08857-118">Самый простой вызов `fetch` принимает один параметр, представляющий маршрут.</span><span class="sxs-lookup"><span data-stu-id="08857-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="08857-119">Второй параметр (объект `init`) является необязательным.</span><span class="sxs-lookup"><span data-stu-id="08857-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="08857-120">`init` используется для настройки запроса HTTP.</span><span class="sxs-lookup"><span data-stu-id="08857-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="08857-121">Настройте в приложении [обслуживание статических файлов](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) и [включите сопоставление файлов по умолчанию](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="08857-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="08857-122">Вставьте в метод `Configure` в файле *Startup.cs* следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="08857-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="08857-123">Создайте папку *wwwroot* в корневом каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="08857-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="08857-124">Создайте папку *js* в папке *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="08857-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="08857-125">Добавьте HTML-файл *index.html* в папку *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="08857-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="08857-126">Замените содержимое файла *index.html* следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="08857-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="08857-127">Добавьте CSS-файл с именем *site.css* в папку *wwwroot/css*.</span><span class="sxs-lookup"><span data-stu-id="08857-127">Add a CSS file named *site.css* to the *wwwroot/css* folder.</span></span> <span data-ttu-id="08857-128">Замените содержимое файла *site.css* следующими стилями:</span><span class="sxs-lookup"><span data-stu-id="08857-128">Replace the contents of *site.css* with the following styles:</span></span>

    [!code-css[](first-web-api/samples/3.0/TodoApi/wwwroot/css/site.css)]

1. <span data-ttu-id="08857-129">Добавьте файл JavaScript с именем *site.js* в папку *wwwroot/js*.</span><span class="sxs-lookup"><span data-stu-id="08857-129">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="08857-130">Замените содержимое файла *site.js* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="08857-130">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="08857-131">Может потребоваться изменение параметров запуска проекта ASP.NET Core для локального тестирования HTML-страницы:</span><span class="sxs-lookup"><span data-stu-id="08857-131">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="08857-132">Откройте файл *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="08857-132">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="08857-133">Удалите свойство `launchUrl`, чтобы приложение открылось через *index.html* &mdash; файл проекта по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="08857-133">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="08857-134">В этом примере вызываются все методы CRUD в веб-API.</span><span class="sxs-lookup"><span data-stu-id="08857-134">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="08857-135">Ниже приводится пояснение запросов веб-API.</span><span class="sxs-lookup"><span data-stu-id="08857-135">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="08857-136">Получение списка элементов задач</span><span class="sxs-lookup"><span data-stu-id="08857-136">Get a list of to-do items</span></span>

<span data-ttu-id="08857-137">В следующем коде HTTP-запрос GET направляется по пути *api/TodoItems*:</span><span class="sxs-lookup"><span data-stu-id="08857-137">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="08857-138">Когда веб-API возвращает код состояния, указывающий на успешное выполнение, вызывается функция `_displayItems`.</span><span class="sxs-lookup"><span data-stu-id="08857-138">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="08857-139">Каждый элемент списка задач в параметре массива, который принимается `_displayItems`, добавляется в таблицу с помощью кнопок **Изменить** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="08857-139">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="08857-140">Если запрос веб-API завершается сбоем, в консоли браузера регистрируется сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="08857-140">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="08857-141">Добавление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="08857-141">Add a to-do item</span></span>

<span data-ttu-id="08857-142">В приведенном ниже коде выполняется следующее:</span><span class="sxs-lookup"><span data-stu-id="08857-142">In the following code:</span></span>

* <span data-ttu-id="08857-143">Переменная `item` объявляется для создания представления объектного литерала элемента списка задач.</span><span class="sxs-lookup"><span data-stu-id="08857-143">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="08857-144">Для запроса Fetch настраиваются следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="08857-144">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="08857-145">`method` определяет команду действия HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="08857-145">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="08857-146">`body` определяет представление JSON текста запроса.</span><span class="sxs-lookup"><span data-stu-id="08857-146">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="08857-147">JSON создается путем передачи литерала объекта, хранящегося в `item`, в функцию [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="08857-147">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="08857-148">`headers` определяет заголовки `Accept` и `Content-Type` запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="08857-148">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="08857-149">Для обеих параметров устанавливается значение `application/json`, чтобы классифицировать тип носителя при получении и отправке соответственно.</span><span class="sxs-lookup"><span data-stu-id="08857-149">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="08857-150">HTTP-запрос POST направляется по пути *api/TodoItems*.</span><span class="sxs-lookup"><span data-stu-id="08857-150">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="08857-151">Когда веб-API возвращает код состояния, указывающий на успешное выполнение, вызывается функция `getItems` для обновления таблицы HTML.</span><span class="sxs-lookup"><span data-stu-id="08857-151">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="08857-152">Если запрос веб-API завершается сбоем, в консоли браузера регистрируется сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="08857-152">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="08857-153">Обновление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="08857-153">Update a to-do item</span></span>

<span data-ttu-id="08857-154">Обновление элемента списка задач аналогично его добавлению. Но есть два существенных отличия:</span><span class="sxs-lookup"><span data-stu-id="08857-154">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="08857-155">Путь имеет суффикс с уникальным идентификатором обновляемого элемента.</span><span class="sxs-lookup"><span data-stu-id="08857-155">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="08857-156">Например, *api/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="08857-156">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="08857-157">Команда действия HTTP — это PUT, как указано в параметре `method`.</span><span class="sxs-lookup"><span data-stu-id="08857-157">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="08857-158">Удаление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="08857-158">Delete a to-do item</span></span>

<span data-ttu-id="08857-159">Чтобы удалить элемент списка задач, укажите для параметра запроса `method` значение `DELETE` и определите уникальный идентификатор элемента в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="08857-159">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="08857-160">Перейдите к следующему руководству, в котором описано, как создавать страницы справки по веб-API:</span><span class="sxs-lookup"><span data-stu-id="08857-160">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
