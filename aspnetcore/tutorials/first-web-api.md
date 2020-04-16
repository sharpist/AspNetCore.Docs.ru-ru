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
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Учебник. Создание веб-API с помощью ASP.NET Core

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Майк Уоссон](https://github.com/mikewasson) (Mike Wasson)

В этом учебном курсе приводятся основные сведения о создании веб-API с помощью ASP.NET Core.

::: moniker range=">= aspnetcore-3.0"

В этом руководстве вы узнаете, как:

> [!div class="checklist"]
> * Создать проект веб-API.
> * Добавить класс модели и контекст базы данных.
> * Сформировать обвязку контроллера с использованием методов CRUD.
> * Настроить маршрутизацию, URL-пути и возвращаемые значения.
> * Вызвать веб-API с помощью Postman.

В итоге вы получите веб-API, позволяющий работать с элементами списка запланированных дел (задач), хранящихся в базе данных.

## <a name="overview"></a>Общая схема

В этом руководстве создается следующий API-интерфейс:

|API | Описание | Тело запроса | Тело ответа |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Получение всех элементов списка задач | Отсутствует | Массив элементов списка задач |
|GET /api/TodoItems/{id} | Получение элемента по идентификатору | Отсутствует | Элемент списка задач |
|POST /api/TodoItems | Добавление нового элемента | Элемент списка задач | Элемент списка задач |
|PUT /api/TodoItems/{id} | Обновление существующего элемента &nbsp; | Элемент списка задач | Отсутствует |
|DELETE /api/TodoItems/{id} &nbsp; &nbsp; | Удаление существующего элемента &nbsp; &nbsp; | Отсутствует | Отсутствует |

На рисунке ниже показана структура приложения.

![Клиент представлен прямоугольником слева. Он отправляет запрос и получает ответ от приложения (прямоугольник справа). В прямоугольнике приложения также расположены три прямоугольника, представляющих контроллер, модель и уровень доступа к данным. Запрос поступает на контроллер приложения, который выполняет операции чтения и записи между контроллером и уровнем доступа к данным. Модель сериализуется и возвращается клиенту в ответе.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>Создание веб-проекта

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В меню **Файл** выберите пункт **Создать** > **Проект**.
* Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.
* Назовите проект *TodoApi* и нажмите **Создать**.
* В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**. Выберите шаблон **API** и нажмите кнопку **Создать**.

![Диалоговое окно создания проекта VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.
* Выполните следующие команды:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.

  Предыдущие команды:

  * Создает новый проект веб-API и открывает его в Visual Studio Code.
  * Добавляет пакеты NuGet, которые понадобятся в следующем разделе.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните **Файл** > **Новое решение**.

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* Щелкните **.NET Core** > **Приложение** > **API** > **Далее**.

  ![Диалоговое окно "Новый проект" в macOS](first-web-api-mac/_static/1.png)
  
* В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **Целевой платформы** выберите * *.NET Core 3.1*.

* Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Откройте командный терминал в папке проекта и выполните следующие команды:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Проверка API

Шаблон проекта создает API `WeatherForecast`. Вызовите метод `Get` из браузера для проверки работоспособности приложения.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Нажмите клавиши CTRL+F5, чтобы запустить приложение. Visual Studio запустит браузер и перейдет к `https://localhost:<port>/WeatherForecast`, где `<port>` — это номер порта, выбранный случайным образом.

Если появится диалоговое окно с предложением сделать сертификат IIS Express доверенным, выберите **Да**. В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Нажмите клавиши CTRL+F5, чтобы запустить приложение. В браузере перейдите по следующему URL-адресу: `https://localhost:5001/WeatherForecast`.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение. Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом. Появится ошибка HTTP 404 (Не найдено). Добавьте `/WeatherForecast` к URL-адресу (измените URL-адрес на `https://localhost:<port>/WeatherForecast`).

---

Возвращаемые данные JSON будут выглядеть примерно так:

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

## <a name="add-a-model-class"></a>Добавление класса модели

*Модель* — это набор классов, представляющих данные, которыми управляет приложение. Модель этого приложения содержит единственный класс `TodoItem`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В **обозревателе решений** щелкните проект правой кнопкой мыши. Выберите **Добавить** > **Новая папка**. Присвойте папке имя *Models*.

* Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**. Присвойте классу имя *TodoItem* и нажмите **Добавить**.

* Замените шаблонный код следующим кодом:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Добавьте папку с именем *Models*.

* Добавьте класс `TodoItem` в папку *Models*, используя следующий код:

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните проект правой кнопкой мыши. Выберите **Добавить** > **Новая папка**. Присвойте папке имя *Models*.

  ![Новая папка](first-web-api-mac/_static/folder.png)

* Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.

* Назовите класс *TodoItem* и нажмите **Создать**.

* Замените шаблонный код следующим кодом:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.

Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models*.

## <a name="add-a-database-context"></a>Добавление контекста базы данных

*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных. Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>Добавление Microsoft.EntityFrameworkCore.SqlServer

* В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.
* Перейдите на вкладку **Обзор** и введите **Microsoft.EntityFrameworkCore.SqlServer** в поле поиска.
* На панели слева выберите **Microsoft.EntityFrameworkCore.SqlServer**.
* Установите флажок **Проект** на правой панели и нажмите **Установить**.
* Аналогичным образом добавьте NuGet-пакет `Microsoft.EntityFrameworkCore.InMemory`.

![Диспетчер пакетов NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Добавление контекста базы данных TodoContext

* Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**. Назовите класс *TodoContext* и нажмите **Добавить**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

* Добавьте класс `TodoContext` в папку *Models*.

---

* Вставьте следующий код:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Регистрация контекста базы данных

Службы (такие как контекст базы данных) должны быть зарегистрированы в ASP.NET Core с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection). Контейнер предоставляет службу контроллерам.

Внесите изменения в файл *Startup.cs*, используя следующий выделенный код:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

В вышеприведенном коде выполнено:

* Удаление неиспользуемых объявлений `using`.
* Добавление контекста базы данных в контейнер внедрения зависимостей.
* Указание, что контекст базы данных будет использовать базу данных в памяти.

## <a name="scaffold-a-controller"></a>Формирование обвязки контроллера

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Щелкните папку *Controllers* правой кнопкой мыши.
* Выберите **Добавить** > **Создать шаблонный элемент**.
* Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем нажмите **Добавить**.
* В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** выполните следующее:

  * В поле **Класс модели** выберите **TodoItem (TodoApi.Models)**.
  * В поле **Класс контекста данных** выберите **TodoContext (TodoApi.Models)**.
  * Нажмите **Добавить**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

Выполните следующие команды:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Предыдущие команды:

* добавляют пакеты NuGet, необходимые для формирования шаблонов;
* устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);
* формируют шаблоны для `TodoItemsController`.

---

Сформированный код:

* Помечает этот класс атрибутом [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute). Этот атрибут указывает, что контроллер отвечает на запросы веб-API. Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.
* Вставляет контекст базы данных (`TodoContext`) в контроллер с помощью механизма внедрения зависимостей. Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления (CRUD)](https://wikipedia.org/wiki/Create,_read,_update_and_delete) контроллера.

Маршрутные маски ASP.NET Core:

* Для контроллеров с представлениями содержат `[action]` в маршрутной маске.
* Для API-контроллеров не содержат `[action]` в маршрутной маске.

Если признак `[action]` отсутствует в маршрутной маске, то имя [действия](xref:mvc/controllers/routing#action) из нее исключается. То есть в таком маршруте не используется сопоставленный с именем действия метод.

## <a name="examine-the-posttodoitem-create-method"></a>Знакомство с методом создания элемента PostTodoItem

Замените инструкцию возврата в `PostTodoItem` на новую с использованием оператора [nameof](/dotnet/csharp/language-reference/operators/nameof):

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute). Этот метод получает значение элемента списка задач из тела HTTP-запроса.

Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:

* В случае успеха возвращает код состояния HTTP 201. HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающего ресурс на сервере.
* Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location). Заголовок `Location` содержит [URI-ссылку](https://developer.mozilla.org/docs/Glossary/URI) на только что созданный элемент списка задач. Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Использует имя действия `GetTodoItem` для создания URI в заголовке `Location`. Ключевое слово `nameof` языка C# используется для предотвращения прямого указания имени действия при вызове `CreatedAtAction`.

### <a name="install-postman"></a>Установка Postman

В этом учебнике для проверки веб-API используется Postman.

* Установите [Postman](https://www.getpostman.com/downloads/)
* Запустите веб-приложение.
* Запустите Postman.
* Отключите параметр **Проверка SSL-сертификата**
  * В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.
    > [!WARNING]
    > После проверки контроллера снова включите проверку SSL-сертификата.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Проверка PostTodoItem с помощью Postman

* Создайте новый запрос.
* Установите HTTP-метод `POST`.
* Откройте вкладку **Тело**.
* Установите переключатель **без обработки**.
* Задайте тип **JSON (приложение/json)** .
* В теле запроса введите код JSON для элемента списка задач:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Нажмите кнопку **Отправить**.

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Проверка URI из заголовка Loacation

* Перейдите на вкладку **Заголовки** в области **Ответ**.
* Скопируйте значение заголовка **Location**:

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* Выберите метод GET.
* Вставьте URI (например, `https://localhost:5001/api/TodoItems/1`).
* Нажмите кнопку **Отправить**.

## <a name="examine-the-get-methods"></a>Знакомство с методами GET

Эти методы осуществляются через две конечные точки GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Проверьте приложение, вызвав эти две конечные точки в браузере или в Postman. Например:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

При вызове `GetTodoItems` возвращается примерно такой ответ:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Проверка Get с помощью Postman

* Создайте новый запрос.
* Выберите HTTP-метод **GET**.
* Укажите URL-адрес запроса `https://localhost:<port>/api/TodoItems`. Например, `https://localhost:5001/api/TodoItems`.
* Выберите в Postman режим **Представление с двумя областями**.
* Нажмите кнопку **Отправить**.

Наше приложение использует выполняющуюся в памяти базу данных. Если остановить и вновь запустить его, вышеописанный запрос GET не возвратит никаких данных. Если ничего не возвращается, отправьте данные в приложение методом [POST](#post).

## <a name="routing-and-url-paths"></a>Маршруты обработки запросов и URL-пути

Атрибут [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) свидетельствует о том, что метод отвечает на запрос HTTP GET. URL-путь для каждого метода складывается следующим образом:

* Сначала идет маска из атрибута `Route` контроллера:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Вместо `[controller]` подставляется имя контроллера (по соглашению это имя класса контроллера без суффикса "Controller"). В этом примере класс контроллера именуется **TodoItems**Controller, а сам контроллер, соответственно, — "TodoItems". [Маршруты обработки запросов](xref:mvc/controllers/routing) в ASP.NET Core не чувствительны к регистру символов.
* Если атрибут `[HttpGet]` имеет маршрутную маску (например, `[HttpGet("products")]`), то она добавляется к пути. В данном примере маска не используется. Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью признака Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

В следующем методе `GetTodoItem` есть метка `"{id}"` - это переменная-заполнитель, она указывает на местоположение уникального идентификатора элемента задачи в адресе. При вызове метода `GetTodoItem` параметру `id` присваивается значение `"{id}"` из URL-адреса.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Возвращаемые значения

Возвращаемое значение для методов `GetTodoItems` и `GetTodoItem` имеет тип [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и помещает его в тело ответного сообщения. Ответ такого типа имеет код 200, что свидетельствует об отсутствии необработанных исключений. Необработанные исключения преобразуются в ошибки с кодом 5xx.

Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP. Например, метод `GetTodoItem` может возвращать два разных значения состояния:

* Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку с кодом 404 ([NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)).
* В остальных случаях метод возвращает ответ с кодом 200 и телом в формате JSON. Возвращаемый элемент `item` приходит в ответом сообщении с кодом HTTP 200.

## <a name="the-puttodoitem-method"></a>Метод PutTodoItem

Рассмотрим метод `PutTodoItem`.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

Метод `PutTodoItem` аналогичен методу `PostTodoItem`, но использует в HTTP-запросе PUT. Ответ — [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял обновляемую сущность целиком, а не только ту часть, которую планируется изменить, т.е. по сути это полная замена существующего элемента новым. Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Если при вызове `PutTodoItem` возникнет ошибка, вызовите `GET`, чтобы убедиться, что в базе данных существует такой элемент, который предполагается обновить.

### <a name="test-the-puttodoitem-method"></a>Проверка метода PutTodoItem

В этом примере используется база данных, работающая в памяти, которая инициируется при каждом запуске приложения, начиная с чистого листа. К моменту вызова PUT в базе данных уже должен существовать какой-либо элемент. Поэтому перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.

Обновление элемента списка задач с идентификатором 1 и присвоение ему имени "feed fish":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

На следующем рисунке показан процесс обновления с помощью Postman:

![Консоль Postman с ответом 204 (No Content)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Метод DeleteTodoItem

Рассмотрим метод `DeleteTodoItem`.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Проверка метода DeleteTodoItem

Удаление элемента из списка задач с помощью Postman:

* Выберите метод `DELETE`.
* Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).
* Нажмите кнопку **Отправить**.

<a name="over-post"></a>

## <a name="prevent-over-posting"></a>Предотвращение избыточной публикации

На данный момент наше приложение публикует наружу весь объект `TodoItem` целиком. В рабочей среде приложения обычно ограничивают входящие и исходящие данные за счет использования подмножеств (сегментов) модели. Это связано с целым рядом причин, основная из которых - безопасность. Подмножество модели обычно называется объектом переноса данных (Data Transfer Object или DTO), моделью входящих данных или моделью представления. В этой статье используется **DTO**.

DTO можно использовать чтобы:

* Предотвратить избыточную публикацию.
* Скрыть свойства, которые не предназначены для просмотра клиентами.
* Исключить некоторые свойства, уменьшив тем самым объем полезных данных.
* Получить плоские графы объектов, содержащих вложенные объекты. Плоские графы объектов могут быть более оптимальны для клиентов.

Чтобы продемонстрировать подход с применением DTO, отредактируйте класс `TodoItem`, добавив в него секретное поле:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

Секретное поле должно быть скрыто в этом приложении, однако административное приложение может отобразить его.

Убедитесь, что вы можете отправить и получить секретное поле.

Создайте модель DTO:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Измените `TodoItemsController` для возможности использования `TodoItemDTO`:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Убедитесь, что вы не можете отправить или получить секретное поле.

## <a name="call-the-web-api-with-javascript"></a>Вызов веб-API с помощью JavaScript

См. руководство по [Вызову веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В этом руководстве вы узнаете, как:

> [!div class="checklist"]
> * Создание проекта веб-API.
> * Добавление класса модели и контекста базы данных.
> * Добавление контроллера.
> * Добавление методов CRUD.
> * Настройка маршрутизации и путей URL.
> * Указание возвращаемых значений.
> * Вызов веб-API с помощью Postman.
> * Вызовите веб-API с помощью JavaScript.

В конечном итоге вы получите веб-API, обеспечивающий управление элементами списка дел, хранящимися в реляционной базе данных.

## <a name="overview"></a>Обзор

В этом руководстве создается следующий API-интерфейс:

|API | Описание | Текст запроса | Текст ответа |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Получение всех элементов задач | Отсутствуют | Массив элементов задач|
|GET /api/TodoItems/{id} | Получение объекта по идентификатору | Отсутствуют | Элемент задачи|
|POST /api/TodoItems | Добавление нового элемента | Элемент задачи | Элемент задачи |
|PUT /api/TodoItems/{id} | Обновление существующего элемента &nbsp; | Элемент задачи | Отсутствуют |
|DELETE /api/TodoItems/{id} &nbsp; &nbsp; | Удаление элемента &nbsp; &nbsp; | Отсутствуют | Отсутствуют|

На следующем рисунке показана структура приложения.

![Клиент представлен прямоугольником слева. Он отправляет запрос и получает ответ от приложения (прямоугольник справа). В прямоугольнике приложения также расположены три прямоугольника, представляющих контроллер, модель и уровень доступа к данным. Запрос поступает на контроллер приложения, который выполняет операции чтения и записи между контроллером и уровнем доступа к данным. Модель сериализуется и возвращается клиенту в ответе.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>Создайте веб-проект.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В меню **Файл** выберите пункт **Создать** > **Проект**.
* Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.
* Назовите проект *TodoApi* и нажмите **Создать**.
* В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 2.2**. Выберите шаблон **API** и нажмите кнопку **Создать**. **Не** выбирайте **Включение поддержки Docker**.

![Диалоговое окно создания проекта VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.
* Выполните следующие команды:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  С помощью этих команд создается новый проект веб-API и открывается новый экземпляр Visual Studio Code в новой папке проекта.

* При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните **Файл** > **Новое решение**.

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* Щелкните **.NET Core** > **Приложение** > **API** > **Далее**.

  ![Диалоговое окно "Новый проект" в macOS](first-web-api-mac/_static/1.png)
  
* В диалоговом окне **Настройка нового веб-API ASP.NET Core** оставьте установленное по умолчанию значение для параметра **Целевая платформа**, то есть * *.NET Core 2.2*.

* Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>Тестирование API

Шаблон проекта создает API `values`. Вызовите метод `Get` из браузера для тестирования приложения.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Нажмите клавиши CTRL+F5, чтобы запустить приложение. Visual Studio запустит браузер и перейдет к `https://localhost:<port>/api/values`, где `<port>` — это номер порта, выбранный случайным образом.

Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**. В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Нажмите клавиши CTRL+F5, чтобы запустить приложение. В браузере перейдите по следующему URL-адресу: `https://localhost:5001/api/values`.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение. Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом. Появится ошибка HTTP 404 (Не найдено). Добавьте `/api/values` к URL-адресу (измените URL-адрес на `https://localhost:<port>/api/values`).

---

Возвращается следующий файл JSON:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Добавление класса модели

*Модель* — это набор классов, представляющих данные, которыми управляет приложение. Модель этого приложения содержит единственный класс `TodoItem`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В **обозревателе решений** щелкните проект правой кнопкой мыши. Выберите **Добавить** > **Новая папка**. Присвойте папке имя *Models*.

* Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**. Присвойте классу имя *TodoItem* и выберите **Добавить**.

* Замените код шаблона следующим кодом:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Добавьте папку с именем *Models*.

* Добавьте класс `TodoItem` в папку *Models*, используя следующий код:

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните проект правой кнопкой мыши. Выберите **Добавить** > **Новая папка**. Присвойте папке имя *Models*.

  ![Новая папка](first-web-api-mac/_static/folder.png)

* Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.

* Назовите класс *TodoItem* и нажмите **Создать**.

* Замените код шаблона следующим кодом:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.

Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models*.

## <a name="add-a-database-context"></a>Добавление контекста базы данных

*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных. Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Класс**. Назовите класс *TodoContext* и нажмите **Добавить**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

* Добавьте класс `TodoContext` в папку *Models*.

---

* Замените код шаблона следующим кодом:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Регистрация контекста базы данных

В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection). Контейнер предоставляет службу контроллерам.

Обновите файл *Startup.cs*, используя следующий выделенный код:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Предыдущий код:

* Удалите неиспользуемые объявления `using`.
* Добавляет контекст базы данных в контейнер внедрения зависимостей.
* Указывает, что контекст базы данных будет использовать базу данных в памяти.

## <a name="add-a-controller"></a>Добавление контроллера

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Щелкните папку *Controllers* правой кнопкой мыши.
* Щелкните **Добавить** > **Создать элемент**.
* В диалоговом окне **Добавить новый элемент** выберите шаблон **Класс контроллера API**.
* Присвойте классу имя *TodoController* и выберите **Добавить**.

  ![Диалоговое окно добавления элемента с контроллером в поле поиска и выбранным контроллером веб-API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

* В папке *Controllers* создайте класс с именем `TodoController`.

---

* Замените код шаблона следующим кодом:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Предыдущий код:

* Определяет класс контроллера API без методов.
* Пометьте этот класс атрибутом [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute). Этот атрибут указывает, что контроллер отвечает на запросы веб-API. Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.
* Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер. Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.
* Добавляет элемент `Item1` в базу данных, если она пуста. Этот код находится в конструкторе и выполняется каждый раз при обнаружении нового HTTP-запроса. Если вы удалите все элементы, конструктор создаст `Item1` при следующем вызове метода API. Поэтому может создаться впечатление, что удаление не было выполнено, хотя это не так.

## <a name="add-get-methods"></a>Добавление методов Get

Чтобы получить API, который извлекает элементы списка дел, добавьте следующие методы в класс `TodoController`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Эти методы реализуют две конечные точки GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Если приложение все еще выполняется, оно останавливается. Затем оно запускается снова с последними изменениями.

Протестируйте приложение, вызвав эти две конечные точки в браузере. Пример:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

При вызове `GetTodoItems` возвращается следующий ответ HTTP:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>Маршрутизация и пути URL

Атрибут [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) обозначает метод, который отвечает на запрос HTTP GET. Путь URL для каждого метода формируется следующим образом:

* Возьмите строку шаблона в атрибуте `Route` контроллера:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller"). В этом примере класс контроллера носит имя **Todo**, а сам контроллер, соответственно, — "todo". В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.
* Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути. В этом примере шаблон не используется. Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи. При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Возвращаемые значения

Возвращаемое значение имеет тип `GetTodoItems`, а метод `GetTodoItem` имеет тип [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа. Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений. Необработанные исключения преобразуются в ошибки 5xx.

Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP. Например, метод `GetTodoItem` может возвращать два разных значения состояния:

* Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 ([Не найдено](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)).
* В противном случае метод возвращает код 200 с телом ответа JSON. При возвращении `item` возвращается ответ HTTP 200.

## <a name="test-the-gettodoitems-method"></a>Тестирование метода GetTodoItems

В этом учебнике для тестирования веб-API используется Postman.

* Установите [Postman](https://www.getpostman.com/downloads/).
* Запустите веб-приложение.
* Запустите Postman.
* Отключите параметр **Проверка SSL-сертификата**.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

* В меню **Postman** > **Настройки** (вкладка **Общие**) отключите параметр **Проверка SSL-сертификата**. Кроме того, можно выбрать значок гаечного ключа и выбрать **Параметры**, а затем отключить проверку SSL-сертификата.

---
  
> [!WARNING]
> После тестирования контроллера снова включите проверку SSL-сертификата.

* Создайте новый запрос.
  * Укажите метод HTTP **GET**.
  * Укажите URL-адрес запроса `https://localhost:<port>/api/todo`. Например, `https://localhost:5001/api/todo`.
* Выберите режим **Представление с двумя областями** в Postman.
* Нажмите кнопку **Отправить**.

![Postman с запросом Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Добавление метода Create

Добавьте следующий метод `PostTodoItem` в *Controllers/TodoController.cs*: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute). Этот метод получает значение элемента списка дел из текста HTTP-запроса.

Метод `CreatedAtAction`:

* В случае успеха возвращает код состояния HTTP 201. HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.
* Добавляет заголовок `Location` в ответ. Заголовок `Location` расположения указывает URI вновь созданной задачи. Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Указывает действие `GetTodoItem` для создания URI заголовка `Location`. Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>Тестирование метода PostTodoItem

* Выполните построение проекта.
* В Postman укажите метод HTTP `POST`.
* Откройте вкладку **Тело**.
* Установите переключатель **без обработки**.
* Задайте тип **JSON (приложение/json)** .
* В теле запроса введите код JSON для элемента списка дел:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Нажмите кнопку **Отправить**.

  ![Postman с запросом Create](first-web-api/_static/create.png)

  Если вы получаете ошибку 405 "Недопустимый метод", это может свидетельствовать о том, что после добавления метода `PostTodoItem` не была выполнена компиляция проекта.

### <a name="test-the-location-header-uri"></a>Тестирование URI заголовка расположения

* Перейдите на вкладку **Заголовки** в области **Ответ**.
* Скопируйте значение заголовка **Расположение**:

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/pmc2.png)

* Укажите метод GET.
* Вставьте URI (например, `https://localhost:5001/api/Todo/2`).
* Нажмите кнопку **Отправить**.

## <a name="add-a-puttodoitem-method"></a>Добавление метода PutTodoItem

Добавьте приведенный ниже метод `PutTodoItem`.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT. Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения. Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.

### <a name="test-the-puttodoitem-method"></a>Тестирование метода PutTodoItem

В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения. При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент. Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.

Обновите элемент списка дел с идентификатором = 1 и присвойте ему имя "feed fish":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

На следующем рисунке показан процесс обновления Postman:

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>Добавление метода DeleteTodoItem

Добавьте приведенный ниже метод `DeleteTodoItem`.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem`Ответ[ — 204 (нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Тестирование метода DeleteTodoItem

Удалите элемент списка дел с помощью Postman:

* Укажите метод `DELETE`.
* Укажите URI удаляемого объекта (например, `https://localhost:5001/api/todo/1`).
* Нажмите кнопку **Отправить**.

В этом примере приложения вы можете удалить все элементы. Однако в случае удаления последнего элемента в момент следующего вызова API конструктор класса модели создаст новый элемент.

## <a name="call-the-web-api-with-javascript"></a>Вызов веб-API с помощью JavaScript

В этом разделе описано, как добавить HTML-страницу, которая использует JavaScript для вызова веб-API. jQuery инициирует запрос. JavaScript изменяет страницу, используя сведения из ответа API.

Настройте приложение для [обслуживания статических файлов](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) и [включения сопоставления файлов по умолчанию](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), обновив *Startup.cs* следующим выделенным кодом:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Создайте папку *wwwroot* в каталоге проекта.

Добавьте HTML-файл *index.html* в каталог *wwwroot*. Замените его содержимое следующей разметкой:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Добавьте файл JavaScript с именем *site.js* в каталог *wwwroot*. Замените его содержимое следующим кодом:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Может потребоваться изменение параметров запуска проекта ASP.NET Core для локального тестирования HTML-страницы:

* Откройте файл *Properties\launchSettings.json*.
* Удалите свойство `launchUrl`, чтобы приложение открылось через *index.html* &mdash; файл проекта по умолчанию.

В этом примере вызываются все методы CRUD в веб-API. Ниже приводится пояснение вызовов API.

### <a name="get-a-list-of-to-do-items"></a>Получение списка элементов задач

jQuery отправляет запрос HTTP GET к веб-API, который возвращает ответ JSON, представляющий массив элементов списка дел. В случае успешного запроса используется функция обратного вызова `success`. При обратном вызове в модель DOM вносятся данные о задачах.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Добавление элемента задачи

jQuery отправляет запрос HTTP POST с элементом списка дел в тексте запроса. Для параметров `accepts` и `contentType` указывается `application/json`, чтобы указать тип носителя при получении и отправке. Элемент списка дел преобразуется в JSON с помощью [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Если интерфейс API возвращает код состояния успешного выполнения, вызывается функция `getData` для обновления HTML-таблицы.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Обновление элемента задачи

Обновление элемента списка дел выполняется аналогично его добавлению. `url` изменяется, чтобы добавить уникальный идентификатор для элемента, а в качестве `type` устанавливается `PUT`.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Удаление элемента задачи

Чтобы удалить элемент списка дел, установите для `type` в вызове AJAX значение `DELETE` и укажите уникальный идентификатор элемента в URL-адресе.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a>Добавление поддержки аутентификации в веб-API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a>Дополнительные материалы

[Просмотреть или скачать пример кода для этого учебника](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).

Дополнительные сведения см. в следующих источниках:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Версия руководства на YouTube](https://www.youtube.com/watch?v=TTkhEyGBfAk)
