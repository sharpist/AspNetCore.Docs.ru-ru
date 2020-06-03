---
page_type: sample
description: В этом руководстве показано, как создать веб-API ASP.NET Core с помощью базы данных NoSQL MongoDB.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 95a2a6fcda0a4f7148183981f7dbacd06388329d
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106524"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="3f2ab-102">Создание веб-API с помощью ASP.NET Core и MongoDB</span><span class="sxs-lookup"><span data-stu-id="3f2ab-102">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="3f2ab-103">В этом руководстве описано, как создать веб-API, который выполняет операции создания, чтения, обновления и удаления (CRUD) в базе данных NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="3f2ab-103">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="3f2ab-104">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-104">In this tutorial, you learn how to:</span></span>

* <span data-ttu-id="3f2ab-105">Настройка MongoDB</span><span class="sxs-lookup"><span data-stu-id="3f2ab-105">Configure MongoDB</span></span>
* <span data-ttu-id="3f2ab-106">создать базу данных MongoDB;</span><span class="sxs-lookup"><span data-stu-id="3f2ab-106">Create a MongoDB database</span></span>
* <span data-ttu-id="3f2ab-107">определить коллекцию и схему MongoDB;</span><span class="sxs-lookup"><span data-stu-id="3f2ab-107">Define a MongoDB collection and schema</span></span>
* <span data-ttu-id="3f2ab-108">выполнить операции CRUD MongoDB из веб-API.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-108">Perform MongoDB CRUD operations from a web API</span></span>
* <span data-ttu-id="3f2ab-109">Настройка сериализации JSON</span><span class="sxs-lookup"><span data-stu-id="3f2ab-109">Customize JSON serialization</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3f2ab-110">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="3f2ab-110">Prerequisites</span></span>

* [<span data-ttu-id="3f2ab-111">Пакет SDK для .NET Core 3.0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="3f2ab-111">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="3f2ab-112">[Предварительная версия Visual Studio 2019](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) с рабочей нагрузкой **ASP.NET и веб-разработка**</span><span class="sxs-lookup"><span data-stu-id="3f2ab-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="3f2ab-113">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3f2ab-113">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a><span data-ttu-id="3f2ab-114">Настройка MongoDB</span><span class="sxs-lookup"><span data-stu-id="3f2ab-114">Configure MongoDB</span></span>

<span data-ttu-id="3f2ab-115">Если используется Windows, MongoDB по умолчанию устанавливается в папку *C:\\Program Files\\MongoDB*.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-115">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="3f2ab-116">Добавьте *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* в переменную среды `Path`.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-116">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="3f2ab-117">Это изменение обеспечит доступ к MongoDB из любого места на компьютере разработки.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-117">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="3f2ab-118">В следующих шагах используйте интерфейс mongo Shell, чтобы создать базу данных и коллекции и сохранить документы.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-118">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="3f2ab-119">Дополнительные сведения о командах mongo Shell см. в руководстве по [работе с mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="3f2ab-119">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="3f2ab-120">Выберите папку на компьютере разработки для хранения данных.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-120">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="3f2ab-121">Например, *C:\\BooksData* при работе в Windows.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-121">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="3f2ab-122">Если такого каталога нет, создайте его.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-122">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="3f2ab-123">В mongo Shell нельзя создавать каталоги.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-123">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="3f2ab-124">Откройте командную оболочку.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-124">Open a command shell.</span></span> <span data-ttu-id="3f2ab-125">Выполните следующую команду для подключения к MongoDB через порт 27017, заданный по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-125">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="3f2ab-126">Не забудьте заменить `<data_directory_path>` каталогом, созданным на предыдущем этапе.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-126">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="3f2ab-127">Откройте другой экземпляр командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-127">Open another command shell instance.</span></span> <span data-ttu-id="3f2ab-128">Подключитесь к тестовой базе данных по умолчанию, выполнив такую команду:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-128">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="3f2ab-129">Запустите в командной оболочке следующее:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-129">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="3f2ab-130">Будет создана база данных с именем *BookstoreDb*, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-130">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="3f2ab-131">Если такая база данных существует, для нее уже установлено подключение для транзакций.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-131">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="3f2ab-132">Создайте коллекцию `Books` с помощью такой команды:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-132">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="3f2ab-133">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-133">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="3f2ab-134">Определите схему для коллекции `Books` и вставьте два документа, используя следующую команду:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-134">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="3f2ab-135">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-135">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

  > [!NOTE]
  > <span data-ttu-id="3f2ab-136">Идентификаторы в этой статье не будут соответствовать идентификаторам в запущенном примере.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-136">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="3f2ab-137">Просмотрите документы в базе данных, используя такую команду:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-137">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="3f2ab-138">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-138">The following result is displayed:</span></span>

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    <span data-ttu-id="3f2ab-139">Схема добавляет автоматически созданное свойство `_id` типа `ObjectId` к каждому документу.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-139">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="3f2ab-140">База данных готова к работе.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-140">The database is ready.</span></span> <span data-ttu-id="3f2ab-141">Можно приступить к созданию веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-141">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="3f2ab-142">Создание проекта веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f2ab-142">Create the ASP.NET Core web API project</span></span>

1. <span data-ttu-id="3f2ab-143">Выберите **Файл** > **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-143">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="3f2ab-144">Выберите тип проекта **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-144">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="3f2ab-145">Задайте для проекта имя *BooksApi* и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-145">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="3f2ab-146">Выберите целевую платформу **.NET Core** и **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-146">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="3f2ab-147">Выберите шаблон проекта **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-147">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="3f2ab-148">Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-148">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3f2ab-149">В окне **Консоль диспетчера пакетов** перейдите в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-149">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="3f2ab-150">Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-150">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a><span data-ttu-id="3f2ab-151">Добавление модели сущности</span><span class="sxs-lookup"><span data-stu-id="3f2ab-151">Add an entity model</span></span>

1. <span data-ttu-id="3f2ab-152">Добавьте каталог *Models* в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-152">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="3f2ab-153">Добавьте класс `Book` в каталог *Models* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-153">Add a `Book` class to the *Models* directory with the following code:</span></span>

    ```csharp
    using MongoDB.Bson;
    using MongoDB.Bson.Serialization.Attributes;

    namespace BooksApi.Models
    {
        public class Book
        {
            [BsonId]
            [BsonRepresentation(BsonType.ObjectId)]
            public string Id { get; set; }

            [BsonElement("Name")]
            public string BookName { get; set; }

            public decimal Price { get; set; }

            public string Category { get; set; }

            public string Author { get; set; }
        }
    }
    ```

    <span data-ttu-id="3f2ab-154">В классе выше свойство `Id`:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-154">In the preceding class, the `Id` property:</span></span>

    * <span data-ttu-id="3f2ab-155">требуется для сопоставления объекта среды CLR с коллекцией MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-155">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="3f2ab-156">Помечается с помощью [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) для назначения этого свойства в качестве первичного ключа документа.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-156">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="3f2ab-157">Помечается с помощью [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm), чтобы разрешить передачу параметра в качестве типа `string` вместо структуры [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm).</span><span class="sxs-lookup"><span data-stu-id="3f2ab-157">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="3f2ab-158">Mongo обрабатывает преобразование из `string` в `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-158">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

    <span data-ttu-id="3f2ab-159">Свойство `BookName` помечено атрибутом [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm).</span><span class="sxs-lookup"><span data-stu-id="3f2ab-159">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="3f2ab-160">Значение атрибута `Name` представляет имя свойства в коллекции MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-160">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="3f2ab-161">Добавление модели конфигурации</span><span class="sxs-lookup"><span data-stu-id="3f2ab-161">Add a configuration model</span></span>

1. <span data-ttu-id="3f2ab-162">Добавьте в файл *appsettings.json* перечисленные ниже значения конфигурации базы данных.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-162">Add the following database configuration values to *appsettings.json*:</span></span>

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. <span data-ttu-id="3f2ab-163">Добавьте в каталог *Models* файл *BookstoreDatabaseSettings.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-163">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    <span data-ttu-id="3f2ab-164">Предыдущий класс `BookstoreDatabaseSettings` используется для хранения значений свойств `BookstoreDatabaseSettings` файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-164">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="3f2ab-165">Свойства JSON и C# имеют одинаковые имена, что упрощает сопоставление.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-165">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="3f2ab-166">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-166">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    <span data-ttu-id="3f2ab-167">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-167">In the preceding code:</span></span>

    * <span data-ttu-id="3f2ab-168">Экземпляр конфигурации, к которому привязан раздел `BookstoreDatabaseSettings` файла *appsettings.json*, зарегистрирован в контейнере внедрения зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="3f2ab-168">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="3f2ab-169">Например, свойство `ConnectionString` объекта `BookstoreDatabaseSettings` заполняется свойством `BookstoreDatabaseSettings:ConnectionString` в файле *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-169">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="3f2ab-170">Интерфейс `IBookstoreDatabaseSettings` регистрируется в DI с использованием [времени существования отдельной службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="3f2ab-170">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="3f2ab-171">При внедрении экземпляр интерфейса разрешается в объект `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-171">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="3f2ab-172">Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылки `BookstoreDatabaseSettings` и `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-172">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="3f2ab-173">Добавление службы операций CRUD</span><span class="sxs-lookup"><span data-stu-id="3f2ab-173">Add a CRUD operations service</span></span>

1. <span data-ttu-id="3f2ab-174">Добавьте каталог *Services* в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-174">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="3f2ab-175">Добавьте класс `BookService` в каталог *Services* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-175">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    <span data-ttu-id="3f2ab-176">В предыдущем коде экземпляр `IBookstoreDatabaseSettings` извлекается из DI посредством внедрения конструктора.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-176">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="3f2ab-177">Таким образом обеспечивается доступ к значениям конфигурации в файле *appsettings.json*, которые были добавлены в разделе [Добавление модели конфигурации](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="3f2ab-177">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="3f2ab-178">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-178">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    <span data-ttu-id="3f2ab-179">В предыдущем коде класс `BookService` регистрируется в DI, чтобы обеспечить поддержку внедрения через конструктор в используемые классы.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-179">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="3f2ab-180">Время существования отдельной службы — наиболее подходящий вариант, так как `BookService` имеет прямую зависимость от `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-180">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="3f2ab-181">В соответствии с официальными [правилами повторного использования клиента Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)`MongoClient` следует регистрировать в DI с использованием времени существования отдельной службы.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-181">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="3f2ab-182">Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылку `BookService`:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-182">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>


    ```csharp
    using BooksApi.Services;
    ```

<span data-ttu-id="3f2ab-183">Класс `BookService` использует следующие члены `MongoDB.Driver` для выполнения операций CRUD в базе данных:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-183">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="3f2ab-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): считывает экземпляр сервера для выполнения операций с базой данных.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="3f2ab-185">Конструктор этого класса предоставляет строку подключения MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-185">The constructor of this class is provided the MongoDB connection string:</span></span>

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* <span data-ttu-id="3f2ab-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): представляет базу данных Mongo для выполнения операций.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="3f2ab-187">В этом руководстве используется универсальный метод [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) интерфейса для получения доступа к данным в определенной коллекции.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-187">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="3f2ab-188">Операции CRUD следует выполнять с коллекцией после вызова этого метода.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-188">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="3f2ab-189">В вызове метода `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-189">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="3f2ab-190">`collection` представляет имя коллекции;</span><span class="sxs-lookup"><span data-stu-id="3f2ab-190">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="3f2ab-191">`TDocument` представляет тип объекта среды CLR, хранящегося в коллекции;</span><span class="sxs-lookup"><span data-stu-id="3f2ab-191">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="3f2ab-192">`GetCollection<TDocument>(collection)` возвращает объект [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm), представляющий коллекцию.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-192">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="3f2ab-193">В этом руководстве следующие методы вызываются для коллекции:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-193">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="3f2ab-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): удаляет один документ, отвечающий заданным критериям поиска.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="3f2ab-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): возвращает все документы в коллекции, соответствующие заданным критериям поиска.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="3f2ab-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): вставляет предоставленный объект в виде нового документа в коллекции.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="3f2ab-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): заменяет один документ, отвечающий заданным критериям поиска, предоставленным объектом.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="3f2ab-198">Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="3f2ab-198">Add a controller</span></span>

<span data-ttu-id="3f2ab-199">Добавьте класс `BooksController` в каталог *Controllers* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-199">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

<span data-ttu-id="3f2ab-200">Предыдущий контроллер веб-API:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-200">The preceding web API controller:</span></span>

* <span data-ttu-id="3f2ab-201">использует класс `BookService` для выполнения операций CRUD;</span><span class="sxs-lookup"><span data-stu-id="3f2ab-201">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="3f2ab-202">содержит методы действий для поддержки запросов HTTP GET, POST, PUT и DELETE.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-202">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="3f2ab-203">Вызывает <xref:System.Web.Http.ApiController.CreatedAtRoute*> в методе действия `Create` для возврата ответа [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="3f2ab-203">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="3f2ab-204">Код состояния 201 представляет собой стандартный ответ метода HTTP POST, создающего ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-204">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="3f2ab-205">`CreatedAtRoute` также добавляет заголовок `Location` в ответ.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-205">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="3f2ab-206">Заголовок `Location` указывает универсальный код ресурса (URI) созданной книги.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-206">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="3f2ab-207">Тестирование веб-API</span><span class="sxs-lookup"><span data-stu-id="3f2ab-207">Test the web API</span></span>

1. <span data-ttu-id="3f2ab-208">Выполните сборку и запуск приложения.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-208">Build and run the app.</span></span>

1. <span data-ttu-id="3f2ab-209">Перейдите по адресу `http://localhost:<port>/api/books`, чтобы протестировать не имеющий параметров метод действия `Get` контроллера.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-209">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="3f2ab-210">Отобразится такой ответ JSON:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-210">The following JSON response is displayed:</span></span>

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

1. <span data-ttu-id="3f2ab-211">Перейдите по адресу `http://localhost:<port>/api/books/{id here}`, чтобы протестировать перегруженный метод действия `Get` контроллера.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-211">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="3f2ab-212">Отобразится такой ответ JSON:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-212">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="3f2ab-213">Настройка параметров сериализации JSON</span><span class="sxs-lookup"><span data-stu-id="3f2ab-213">Configure JSON serialization options</span></span>

<span data-ttu-id="3f2ab-214">Нужно изменить два параметра для ответов JSON, возвращаемых в разделе [Тестирование веб-API](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="3f2ab-214">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="3f2ab-215">Смешанный регистр имен свойств по умолчанию следует изменить в соответствии с регистром Pascal имен свойств объекта CLR.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-215">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="3f2ab-216">Свойство `bookName` должно возвращаться как `Name`.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-216">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="3f2ab-217">Чтобы удовлетворить эти требования, внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-217">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="3f2ab-218">JSON.NET удален из общей платформы ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-218">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="3f2ab-219">Добавьте ссылку на пакет для [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="3f2ab-219">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="3f2ab-220">В `Startup.ConfigureServices` вставьте следующий выделенный код в вызов метода `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-220">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    <span data-ttu-id="3f2ab-221">После предыдущего изменения имена свойств в ответе сериализованного JSON веб-API соответствуют именам свойств в типе объекта CLR.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-221">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="3f2ab-222">Например, свойство `Author` класса `Book` сериализуется как `Author`.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-222">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="3f2ab-223">В *Models/Book.cs* добавьте к свойству `BookName` следующий атрибут [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm):</span><span class="sxs-lookup"><span data-stu-id="3f2ab-223">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    <span data-ttu-id="3f2ab-224">Значение атрибута `[JsonProperty]`, равное `Name`, представляет имя свойства в ответе сериализованного JSON веб-API.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-224">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="3f2ab-225">Добавьте следующий код в самое начало файла *Models/Book.cs*, чтобы разрешить ссылку на атрибут `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-225">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    ```csharp
    using Newtonsoft.Json;
    ```

1. <span data-ttu-id="3f2ab-226">Повторите действия, описанные в разделе [Тестирование веб-API](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="3f2ab-226">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="3f2ab-227">Обратите внимание на различие в именах свойств JSON.</span><span class="sxs-lookup"><span data-stu-id="3f2ab-227">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3f2ab-228">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="3f2ab-228">Next steps</span></span>

<span data-ttu-id="3f2ab-229">Дополнительные сведения о сборке веб-API ASP.NET Core см. в этих статьях:</span><span class="sxs-lookup"><span data-stu-id="3f2ab-229">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="3f2ab-230">Версия статьи на YouTube</span><span class="sxs-lookup"><span data-stu-id="3f2ab-230">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [<span data-ttu-id="3f2ab-231">Создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f2ab-231">Create web APIs with ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
