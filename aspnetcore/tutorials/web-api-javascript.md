---
title: Учебник. Вызов веб-API ASP.NET Core с помощью JavaScript
author: rick-anderson
description: Узнайте, как вызывать веб-API ASP.NET Core с помощью JavaScript.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-javascript
ms.openlocfilehash: 4031289e43af75ef2026661dbecbbbce30593d43
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407984"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Учебник. Вызов веб-API ASP.NET Core с помощью JavaScript

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

В этом руководстве описано, как вызвать веб-API ASP.NET Core с помощью JavaScript и [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).

::: moniker range="< aspnetcore-3.0"

Если вы используете ASP.NET Core 2.2, см. соответствующий раздел руководства по [вызову веб-API с помощью JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Предварительные требования

* Изучите [Учебник. Создание веб-API](xref:tutorials/first-web-api).
* Опыт работы с CSS, HTML и JavaScript.

## <a name="call-the-web-api-with-javascript"></a>Вызов веб-API с помощью JavaScript

В этом разделе описано, как добавить HTML-страницу, содержащую формы для создания и администрирования элементов списка задач. Обработчики событий присоединяются к элементам на странице. При использовании обработчиков событий создаются запросы HTTP к методам действия веб-API. Функция Fetch API `fetch` инициирует каждый такой запрос HTTP.

Функция `fetch` возвращает объект [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise), который содержит ответ HTTP, представленный в виде объекта `Response`. Распространенным подходом является извлечение текста ответа JSON путем вызова функции `json` в объекте `Response`. JavaScript изменяет страницу, используя сведения из ответа API.

Самый простой вызов `fetch` принимает один параметр, представляющий маршрут. Второй параметр (объект `init`) является необязательным. `init` используется для настройки запроса HTTP.

1. Настройте в приложении [обслуживание статических файлов](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) и [включите сопоставление файлов по умолчанию](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_). Вставьте в метод `Configure` в файле *Startup.cs* следующий выделенный код:

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. Создайте папку *wwwroot* в корневом каталоге проекта.

1. Создайте папку *js* в папке *wwwroot*.

1. Добавьте HTML-файл *index.html* в папку *wwwroot*. Замените содержимое файла *index.html* следующей разметкой:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. Добавьте файл JavaScript с именем *site.js* в папку *wwwroot/js*. Замените содержимое файла *site.js* следующим кодом:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

Может потребоваться изменение параметров запуска проекта ASP.NET Core для локального тестирования HTML-страницы:

1. Откройте файл *Properties\launchSettings.json*.
1. Удалите свойство `launchUrl`, чтобы приложение открылось через *index.html* &mdash; файл проекта по умолчанию.

В этом примере вызываются все методы CRUD в веб-API. Ниже приводится пояснение запросов веб-API.

### <a name="get-a-list-of-to-do-items"></a>Получение списка элементов задач

В следующем коде HTTP-запрос GET направляется по пути *api/TodoItems*:

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Когда веб-API возвращает код состояния, указывающий на успешное выполнение, вызывается функция `_displayItems`. Каждый элемент списка задач в параметре массива, который принимается `_displayItems`, добавляется в таблицу с помощью кнопок **Изменить** и **Удалить**. Если запрос веб-API завершается сбоем, в консоли браузера регистрируется сообщение об ошибке.

### <a name="add-a-to-do-item"></a>Добавление элемента задачи

В приведенном ниже коде выполняется следующее:

* Переменная `item` объявляется для создания представления объектного литерала элемента списка задач.
* Для запроса Fetch настраиваются следующие параметры:
  * `method` определяет команду действия HTTP POST.
  * `body` определяет представление JSON текста запроса. JSON создается путем передачи литерала объекта, хранящегося в `item`, в функцию [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).
  * `headers` определяет заголовки `Accept` и `Content-Type` запросов HTTP. Для обеих параметров устанавливается значение `application/json`, чтобы классифицировать тип носителя при получении и отправке соответственно.
* HTTP-запрос POST направляется по пути *api/TodoItems*.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Когда веб-API возвращает код состояния, указывающий на успешное выполнение, вызывается функция `getItems` для обновления таблицы HTML. Если запрос веб-API завершается сбоем, в консоли браузера регистрируется сообщение об ошибке.

### <a name="update-a-to-do-item"></a>Обновление элемента задачи

Обновление элемента списка задач аналогично его добавлению. Но есть два существенных отличия:

* Путь имеет суффикс с уникальным идентификатором обновляемого элемента. Например, *api/TodoItems/1*.
* Команда действия HTTP — это PUT, как указано в параметре `method`.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Удаление элемента задачи

Чтобы удалить элемент списка задач, укажите для параметра запроса `method` значение `DELETE` и определите уникальный идентификатор элемента в URL-адресе.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Перейдите к следующему руководству, в котором описано, как создавать страницы справки по веб-API:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
