---
title: Создание веб-API с помощью ASP.NET Core и MongoDB
author: prkhandelwal
description: В этом руководстве показано, как создать веб-API ASP.NET Core с помощью базы данных NoSQL MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mongo-app
ms.openlocfilehash: fac4adb6ffa487c7ca9650a8ebd063dd344d84a7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020253"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="ddaf7-103">Создание веб-API с помощью ASP.NET Core и MongoDB</span><span class="sxs-lookup"><span data-stu-id="ddaf7-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="ddaf7-104">Авторы [Пратик Ханделвал ](https://twitter.com/K2Prk) (Pratik Khandelwal) и [Скотт Эдди](https://twitter.com/Scott_Addie) (Scott Addie).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ddaf7-105">В этом руководстве описано, как создать веб-API, который выполняет операции создания, чтения, обновления и удаления (CRUD) в базе данных NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="ddaf7-106">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ddaf7-107">Настройка MongoDB</span><span class="sxs-lookup"><span data-stu-id="ddaf7-107">Configure MongoDB</span></span>
> * <span data-ttu-id="ddaf7-108">создать базу данных MongoDB;</span><span class="sxs-lookup"><span data-stu-id="ddaf7-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="ddaf7-109">определить коллекцию и схему MongoDB;</span><span class="sxs-lookup"><span data-stu-id="ddaf7-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="ddaf7-110">выполнить операции CRUD MongoDB из веб-API.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="ddaf7-111">Настройка сериализации JSON</span><span class="sxs-lookup"><span data-stu-id="ddaf7-111">Customize JSON serialization</span></span>

<span data-ttu-id="ddaf7-112">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ddaf7-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ddaf7-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="ddaf7-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ddaf7-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ddaf7-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="ddaf7-115">Пакет SDK для .NET Core 3.0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="ddaf7-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="ddaf7-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и веб-разработка**</span><span class="sxs-lookup"><span data-stu-id="ddaf7-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="ddaf7-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ddaf7-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ddaf7-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ddaf7-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="ddaf7-119">Пакет SDK для .NET Core 3.0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="ddaf7-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="ddaf7-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ddaf7-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="ddaf7-121">C# для Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ddaf7-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="ddaf7-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ddaf7-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ddaf7-123">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="ddaf7-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="ddaf7-124">Пакет SDK для .NET Core 3.0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="ddaf7-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="ddaf7-125">Visual Studio для Mac 7.7 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="ddaf7-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="ddaf7-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ddaf7-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="ddaf7-127">Настройка MongoDB</span><span class="sxs-lookup"><span data-stu-id="ddaf7-127">Configure MongoDB</span></span>

<span data-ttu-id="ddaf7-128">Если используется Windows, MongoDB по умолчанию устанавливается в папку *C:\\Program Files\\MongoDB*.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="ddaf7-129">Добавьте *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* в переменную среды `Path`.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="ddaf7-130">Это изменение обеспечит доступ к MongoDB из любого места на компьютере разработки.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="ddaf7-131">В следующих шагах используйте интерфейс mongo Shell, чтобы создать базу данных и коллекции и сохранить документы.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="ddaf7-132">Дополнительные сведения о командах mongo Shell см. в руководстве по [работе с mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="ddaf7-133">Выберите папку на компьютере разработки для хранения данных.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="ddaf7-134">Например, *C:\\BooksData* при работе в Windows.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="ddaf7-135">Если такого каталога нет, создайте его.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="ddaf7-136">В mongo Shell нельзя создавать каталоги.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="ddaf7-137">Откройте командную оболочку.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-137">Open a command shell.</span></span> <span data-ttu-id="ddaf7-138">Выполните следующую команду для подключения к MongoDB через порт 27017, заданный по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="ddaf7-139">Не забудьте заменить `<data_directory_path>` каталогом, созданным на предыдущем этапе.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="ddaf7-140">Откройте другой экземпляр командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-140">Open another command shell instance.</span></span> <span data-ttu-id="ddaf7-141">Подключитесь к тестовой базе данных по умолчанию, выполнив такую команду:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="ddaf7-142">Запустите в командной оболочке следующее:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="ddaf7-143">Будет создана база данных с именем *BookstoreDb*, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="ddaf7-144">Если такая база данных существует, для нее уже установлено подключение для транзакций.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="ddaf7-145">Создайте коллекцию `Books` с помощью такой команды:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="ddaf7-146">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="ddaf7-147">Определите схему для коллекции `Books` и вставьте два документа, используя следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="ddaf7-148">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="ddaf7-149">Идентификаторы в этой статье не будут соответствовать идентификаторам в запущенном примере.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="ddaf7-150">Просмотрите документы в базе данных, используя такую команду:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="ddaf7-151">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="ddaf7-152">Схема добавляет автоматически созданное свойство `_id` типа `ObjectId` к каждому документу.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="ddaf7-153">База данных готова к работе.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-153">The database is ready.</span></span> <span data-ttu-id="ddaf7-154">Можно приступить к созданию веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="ddaf7-155">Создание проекта веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ddaf7-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ddaf7-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ddaf7-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ddaf7-157">Перейдите в раздел **Файл** > **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="ddaf7-158">Выберите тип проекта **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="ddaf7-159">Задайте для проекта имя *BooksApi* и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="ddaf7-160">Выберите целевую платформу **.NET Core** и **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="ddaf7-161">Выберите шаблон проекта **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="ddaf7-162">Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="ddaf7-163">В окне **Консоль диспетчера пакетов** перейдите в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="ddaf7-164">Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ddaf7-165">Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ddaf7-166">В командной оболочке выполните такие команды:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="ddaf7-167">Новый проект веб-API ASP.NET Core для работы с .NET Core будет создан и открыт в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="ddaf7-168">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **Required assets to build and debug are missing from "BooksApi". Add them?** (В BooksApi отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="ddaf7-169">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-169">Select **Yes**.</span></span>
1. <span data-ttu-id="ddaf7-170">Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="ddaf7-171">Откройте **Интегрированный терминал** и перейдите в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="ddaf7-172">Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ddaf7-173">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="ddaf7-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ddaf7-174">В версии Visual Studio для Mac, предшествующей 8.6, на боковой панели выберите **Файл** > **Создать решение** >  **.NET Core** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="ddaf7-175">В версии 8.6 или более поздней на боковой панели выберите **Файл** > **Создать решение** > **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="ddaf7-176">Выберите шаблон проекта **ASP.NET Core** > **API** для C# и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="ddaf7-177">В раскрывающемся списке **Требуемая версия .NET Framework** выберите **.NET Core 3.1** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="ddaf7-178">Введите *BooksApi* в поле **Имя проекта** и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-178">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="ddaf7-179">На панели **Решение** щелкните правой кнопкой мыши узел проекта **Зависимости** и выберите **Добавить пакеты**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="ddaf7-180">В поле поиска введите *MongoDB.Driver*, выберите пакет *MongoDB.Driver* и нажмите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="ddaf7-181">Нажмите кнопку **Принимаю** в диалоговом окне **Принятие условий лицензионного соглашения**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="ddaf7-182">Добавление модели сущности</span><span class="sxs-lookup"><span data-stu-id="ddaf7-182">Add an entity model</span></span>

1. <span data-ttu-id="ddaf7-183">Добавьте каталог *Models* в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="ddaf7-184">Добавьте класс `Book` в каталог *Models* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="ddaf7-185">В классе выше свойство `Id`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="ddaf7-186">требуется для сопоставления объекта среды CLR с коллекцией MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="ddaf7-187">Помечается с помощью [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) для назначения этого свойства в качестве первичного ключа документа.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="ddaf7-188">Помечается с помощью [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm), чтобы разрешить передачу параметра в качестве типа `string` вместо структуры [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="ddaf7-189">Mongo обрабатывает преобразование из `string` в `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="ddaf7-190">Свойство `BookName` помечено атрибутом [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="ddaf7-191">Значение атрибута `Name` представляет имя свойства в коллекции MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="ddaf7-192">Добавление модели конфигурации</span><span class="sxs-lookup"><span data-stu-id="ddaf7-192">Add a configuration model</span></span>

1. <span data-ttu-id="ddaf7-193">Добавьте в файл *appsettings.json* перечисленные ниже значения конфигурации базы данных.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-193">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="ddaf7-194">Добавьте в каталог *Models* файл *BookstoreDatabaseSettings.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="ddaf7-195">Предыдущий класс `BookstoreDatabaseSettings` используется для хранения значений свойств `BookstoreDatabaseSettings` файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="ddaf7-196">Свойства JSON и C# имеют одинаковые имена, что упрощает сопоставление.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="ddaf7-197">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="ddaf7-198">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-198">In the preceding code:</span></span>

   * <span data-ttu-id="ddaf7-199">Экземпляр конфигурации, к которому привязан раздел `BookstoreDatabaseSettings` файла *appsettings.json*, зарегистрирован в контейнере внедрения зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="ddaf7-200">Например, свойство `ConnectionString` объекта `BookstoreDatabaseSettings` заполняется свойством `BookstoreDatabaseSettings:ConnectionString` в файле *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="ddaf7-201">Интерфейс `IBookstoreDatabaseSettings` регистрируется в DI с использованием [времени существования отдельной службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="ddaf7-202">При внедрении экземпляр интерфейса разрешается в объект `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="ddaf7-203">Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылки `BookstoreDatabaseSettings` и `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="ddaf7-204">Добавление службы операций CRUD</span><span class="sxs-lookup"><span data-stu-id="ddaf7-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="ddaf7-205">Добавьте каталог *Services* в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="ddaf7-206">Добавьте класс `BookService` в каталог *Services* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="ddaf7-207">В предыдущем коде экземпляр `IBookstoreDatabaseSettings` извлекается из DI посредством внедрения конструктора.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="ddaf7-208">Таким образом обеспечивается доступ к значениям конфигурации в файле *appsettings.json*, которые были добавлены в разделе [Добавление модели конфигурации](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="ddaf7-209">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="ddaf7-210">В предыдущем коде класс `BookService` регистрируется в DI, чтобы обеспечить поддержку внедрения через конструктор в используемые классы.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="ddaf7-211">Время существования отдельной службы — наиболее подходящий вариант, так как `BookService` имеет прямую зависимость от `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="ddaf7-212">В соответствии с официальными [правилами повторного использования клиента Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)`MongoClient` следует регистрировать в DI с использованием времени существования отдельной службы.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="ddaf7-213">Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылку `BookService`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="ddaf7-214">Класс `BookService` использует следующие члены `MongoDB.Driver` для выполнения операций CRUD в базе данных:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="ddaf7-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): считывает экземпляр сервера для выполнения операций с базой данных.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="ddaf7-216">Конструктор этого класса предоставляет строку подключения MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="ddaf7-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): представляет базу данных Mongo для выполнения операций.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="ddaf7-218">В этом руководстве используется универсальный метод [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) интерфейса для получения доступа к данным в определенной коллекции.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="ddaf7-219">Операции CRUD следует выполнять с коллекцией после вызова этого метода.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="ddaf7-220">В вызове метода `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="ddaf7-221">`collection` представляет имя коллекции;</span><span class="sxs-lookup"><span data-stu-id="ddaf7-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="ddaf7-222">`TDocument` представляет тип объекта среды CLR, хранящегося в коллекции;</span><span class="sxs-lookup"><span data-stu-id="ddaf7-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="ddaf7-223">`GetCollection<TDocument>(collection)` возвращает объект [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm), представляющий коллекцию.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="ddaf7-224">В этом руководстве следующие методы вызываются для коллекции:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="ddaf7-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): удаляет один документ, отвечающий заданным критериям поиска.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="ddaf7-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): возвращает все документы в коллекции, соответствующие заданным критериям поиска.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="ddaf7-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): вставляет предоставленный объект в виде нового документа в коллекции.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="ddaf7-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): заменяет один документ, отвечающий заданным критериям поиска, предоставленным объектом.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="ddaf7-229">Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="ddaf7-229">Add a controller</span></span>

<span data-ttu-id="ddaf7-230">Добавьте класс `BooksController` в каталог *Controllers* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="ddaf7-231">Предыдущий контроллер веб-API:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-231">The preceding web API controller:</span></span>

* <span data-ttu-id="ddaf7-232">использует класс `BookService` для выполнения операций CRUD;</span><span class="sxs-lookup"><span data-stu-id="ddaf7-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="ddaf7-233">содержит методы действий для поддержки запросов HTTP GET, POST, PUT и DELETE.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="ddaf7-234">Вызывает <xref:System.Web.Http.ApiController.CreatedAtRoute*> в методе действия `Create` для возврата ответа [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="ddaf7-235">Код состояния 201 представляет собой стандартный ответ метода HTTP POST, создающего ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="ddaf7-236">`CreatedAtRoute` также добавляет заголовок `Location` в ответ.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="ddaf7-237">Заголовок `Location` указывает универсальный код ресурса (URI) созданной книги.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="ddaf7-238">Тестирование веб-API</span><span class="sxs-lookup"><span data-stu-id="ddaf7-238">Test the web API</span></span>

1. <span data-ttu-id="ddaf7-239">Выполните сборку и запуск приложения.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-239">Build and run the app.</span></span>

1. <span data-ttu-id="ddaf7-240">Перейдите по адресу `http://localhost:<port>/api/books`, чтобы протестировать не имеющий параметров метод действия `Get` контроллера.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="ddaf7-241">Отобразится такой ответ JSON:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-241">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="ddaf7-242">Перейдите по адресу `http://localhost:<port>/api/books/{id here}`, чтобы протестировать перегруженный метод действия `Get` контроллера.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="ddaf7-243">Отобразится такой ответ JSON:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="ddaf7-244">Настройка параметров сериализации JSON</span><span class="sxs-lookup"><span data-stu-id="ddaf7-244">Configure JSON serialization options</span></span>

<span data-ttu-id="ddaf7-245">Нужно изменить два параметра для ответов JSON, возвращаемых в разделе [Тестирование веб-API](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="ddaf7-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="ddaf7-246">Смешанный регистр имен свойств по умолчанию следует изменить в соответствии с регистром Pascal имен свойств объекта CLR.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="ddaf7-247">Свойство `bookName` должно возвращаться как `Name`.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="ddaf7-248">Чтобы удовлетворить эти требования, внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="ddaf7-249">JSON.NET удален из общей платформы ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="ddaf7-250">Добавьте ссылку на пакет для [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="ddaf7-251">В `Startup.ConfigureServices` вставьте следующий выделенный код в вызов метода `AddControllers`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="ddaf7-252">После предыдущего изменения имена свойств в ответе сериализованного JSON веб-API соответствуют именам свойств в типе объекта CLR.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="ddaf7-253">Например, свойство `Author` класса `Book` сериализуется как `Author`.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="ddaf7-254">В *Models/Book.cs* добавьте к свойству `BookName` следующий атрибут [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm):</span><span class="sxs-lookup"><span data-stu-id="ddaf7-254">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="ddaf7-255">Значение атрибута `[JsonProperty]`, равное `Name`, представляет имя свойства в ответе сериализованного JSON веб-API.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="ddaf7-256">Добавьте следующий код в самое начало файла *Models/Book.cs*, чтобы разрешить ссылку на атрибут `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="ddaf7-257">Повторите действия, описанные в разделе [Тестирование веб-API](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="ddaf7-258">Обратите внимание на различие в именах свойств JSON.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ddaf7-259">В этом руководстве описано, как создать веб-API, который выполняет операции создания, чтения, обновления и удаления (CRUD) в базе данных NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="ddaf7-260">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ddaf7-261">Настройка MongoDB</span><span class="sxs-lookup"><span data-stu-id="ddaf7-261">Configure MongoDB</span></span>
> * <span data-ttu-id="ddaf7-262">создать базу данных MongoDB;</span><span class="sxs-lookup"><span data-stu-id="ddaf7-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="ddaf7-263">определить коллекцию и схему MongoDB;</span><span class="sxs-lookup"><span data-stu-id="ddaf7-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="ddaf7-264">выполнить операции CRUD MongoDB из веб-API.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="ddaf7-265">Настройка сериализации JSON</span><span class="sxs-lookup"><span data-stu-id="ddaf7-265">Customize JSON serialization</span></span>

<span data-ttu-id="ddaf7-266">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ddaf7-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ddaf7-267">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="ddaf7-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ddaf7-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ddaf7-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="ddaf7-269">Пакет SDK для .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="ddaf7-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="ddaf7-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и веб-разработка**</span><span class="sxs-lookup"><span data-stu-id="ddaf7-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="ddaf7-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ddaf7-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ddaf7-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ddaf7-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="ddaf7-273">Пакет SDK для .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="ddaf7-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="ddaf7-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ddaf7-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="ddaf7-275">C# для Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ddaf7-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="ddaf7-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ddaf7-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ddaf7-277">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="ddaf7-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="ddaf7-278">Пакет SDK для .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="ddaf7-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="ddaf7-279">Visual Studio для Mac 7.7 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="ddaf7-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="ddaf7-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ddaf7-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="ddaf7-281">Настройка MongoDB</span><span class="sxs-lookup"><span data-stu-id="ddaf7-281">Configure MongoDB</span></span>

<span data-ttu-id="ddaf7-282">Если используется Windows, MongoDB по умолчанию устанавливается в папку *C:\\Program Files\\MongoDB*.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="ddaf7-283">Добавьте *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* в переменную среды `Path`.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="ddaf7-284">Это изменение обеспечит доступ к MongoDB из любого места на компьютере разработки.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="ddaf7-285">В следующих шагах используйте интерфейс mongo Shell, чтобы создать базу данных и коллекции и сохранить документы.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="ddaf7-286">Дополнительные сведения о командах mongo Shell см. в руководстве по [работе с mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="ddaf7-287">Выберите папку на компьютере разработки для хранения данных.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="ddaf7-288">Например, *C:\\BooksData* при работе в Windows.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="ddaf7-289">Если такого каталога нет, создайте его.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="ddaf7-290">В mongo Shell нельзя создавать каталоги.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="ddaf7-291">Откройте командную оболочку.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-291">Open a command shell.</span></span> <span data-ttu-id="ddaf7-292">Выполните следующую команду для подключения к MongoDB через порт 27017, заданный по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="ddaf7-293">Не забудьте заменить `<data_directory_path>` каталогом, созданным на предыдущем этапе.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="ddaf7-294">Откройте другой экземпляр командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-294">Open another command shell instance.</span></span> <span data-ttu-id="ddaf7-295">Подключитесь к тестовой базе данных по умолчанию, выполнив такую команду:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="ddaf7-296">Запустите в командной оболочке следующее:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="ddaf7-297">Будет создана база данных с именем *BookstoreDb*, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="ddaf7-298">Если такая база данных существует, для нее уже установлено подключение для транзакций.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="ddaf7-299">Создайте коллекцию `Books` с помощью такой команды:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="ddaf7-300">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="ddaf7-301">Определите схему для коллекции `Books` и вставьте два документа, используя следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="ddaf7-302">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-302">The following result is displayed:</span></span>

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
   > <span data-ttu-id="ddaf7-303">Идентификаторы в этой статье не будут соответствовать идентификаторам в запущенном примере.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="ddaf7-304">Просмотрите документы в базе данных, используя такую команду:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="ddaf7-305">Отобразится такой результат:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-305">The following result is displayed:</span></span>

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

   <span data-ttu-id="ddaf7-306">Схема добавляет автоматически созданное свойство `_id` типа `ObjectId` к каждому документу.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="ddaf7-307">База данных готова к работе.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-307">The database is ready.</span></span> <span data-ttu-id="ddaf7-308">Можно приступить к созданию веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="ddaf7-309">Создание проекта веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ddaf7-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ddaf7-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ddaf7-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ddaf7-311">Перейдите в раздел **Файл** > **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="ddaf7-312">Выберите тип проекта **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="ddaf7-313">Задайте для проекта имя *BooksApi* и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-313">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="ddaf7-314">Выберите **.NET Core** требуемой версии .NET Framework и **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="ddaf7-315">Выберите шаблон проекта **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="ddaf7-316">Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="ddaf7-317">В окне **Консоль диспетчера пакетов** перейдите в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="ddaf7-318">Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ddaf7-319">Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ddaf7-320">В командной оболочке выполните такие команды:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="ddaf7-321">Новый проект веб-API ASP.NET Core для работы с .NET Core будет создан и открыт в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="ddaf7-322">Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **Required assets to build and debug are missing from "BooksApi". Add them?** (В BooksApi отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="ddaf7-323">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-323">Select **Yes**.</span></span>
1. <span data-ttu-id="ddaf7-324">Посетите страницу [коллекции NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), чтобы узнать последнюю стабильную версию драйвера .NET для MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="ddaf7-325">Откройте **Интегрированный терминал** и перейдите в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="ddaf7-326">Выполните следующую команду, чтобы установить драйвер .NET для MongoDB:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ddaf7-327">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="ddaf7-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ddaf7-328">В версии Visual Studio для Mac, предшествующей 8.6, на боковой панели выберите **Файл** > **Создать решение** >  **.NET Core** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="ddaf7-329">В версии 8.6 или более поздней на боковой панели выберите **Файл** > **Создать решение** > **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="ddaf7-330">Выберите шаблон проекта **Веб-API ASP.NET Core** для C# и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="ddaf7-331">В раскрывающемся списке **Требуемая версия .NET Framework** выберите **.NET Core 2.2** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="ddaf7-332">Введите *BooksApi* в поле **Имя проекта** и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-332">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="ddaf7-333">На панели **Решение** щелкните правой кнопкой мыши узел проекта **Зависимости** и выберите **Добавить пакеты**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="ddaf7-334">В поле поиска введите *MongoDB.Driver*, выберите пакет *MongoDB.Driver* и нажмите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="ddaf7-335">Нажмите кнопку **Принимаю** в диалоговом окне **Принятие условий лицензионного соглашения**.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="ddaf7-336">Добавление модели сущности</span><span class="sxs-lookup"><span data-stu-id="ddaf7-336">Add an entity model</span></span>

1. <span data-ttu-id="ddaf7-337">Добавьте каталог *Models* в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="ddaf7-338">Добавьте класс `Book` в каталог *Models* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="ddaf7-339">В классе выше свойство `Id`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="ddaf7-340">требуется для сопоставления объекта среды CLR с коллекцией MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="ddaf7-341">Помечается с помощью [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) для назначения этого свойства в качестве первичного ключа документа.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="ddaf7-342">Помечается с помощью [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm), чтобы разрешить передачу параметра в качестве типа `string` вместо структуры [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="ddaf7-343">Mongo обрабатывает преобразование из `string` в `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="ddaf7-344">Свойство `BookName` помечено атрибутом [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="ddaf7-345">Значение атрибута `Name` представляет имя свойства в коллекции MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="ddaf7-346">Добавление модели конфигурации</span><span class="sxs-lookup"><span data-stu-id="ddaf7-346">Add a configuration model</span></span>

1. <span data-ttu-id="ddaf7-347">Добавьте в файл *appsettings.json* перечисленные ниже значения конфигурации базы данных.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-347">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="ddaf7-348">Добавьте в каталог *Models* файл *BookstoreDatabaseSettings.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="ddaf7-349">Предыдущий класс `BookstoreDatabaseSettings` используется для хранения значений свойств `BookstoreDatabaseSettings` файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="ddaf7-350">Свойства JSON и C# имеют одинаковые имена, что упрощает сопоставление.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="ddaf7-351">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="ddaf7-352">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-352">In the preceding code:</span></span>

   * <span data-ttu-id="ddaf7-353">Экземпляр конфигурации, к которому привязан раздел `BookstoreDatabaseSettings` файла *appsettings.json*, зарегистрирован в контейнере внедрения зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="ddaf7-354">Например, свойство `ConnectionString` объекта `BookstoreDatabaseSettings` заполняется свойством `BookstoreDatabaseSettings:ConnectionString` в файле *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="ddaf7-355">Интерфейс `IBookstoreDatabaseSettings` регистрируется в DI с использованием [времени существования отдельной службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="ddaf7-356">При внедрении экземпляр интерфейса разрешается в объект `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="ddaf7-357">Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылки `BookstoreDatabaseSettings` и `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="ddaf7-358">Добавление службы операций CRUD</span><span class="sxs-lookup"><span data-stu-id="ddaf7-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="ddaf7-359">Добавьте каталог *Services* в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="ddaf7-360">Добавьте класс `BookService` в каталог *Services* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="ddaf7-361">В предыдущем коде экземпляр `IBookstoreDatabaseSettings` извлекается из DI посредством внедрения конструктора.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="ddaf7-362">Таким образом обеспечивается доступ к значениям конфигурации в файле *appsettings.json*, которые были добавлены в разделе [Добавление модели конфигурации](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="ddaf7-363">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="ddaf7-364">В предыдущем коде класс `BookService` регистрируется в DI, чтобы обеспечить поддержку внедрения через конструктор в используемые классы.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="ddaf7-365">Время существования отдельной службы — наиболее подходящий вариант, так как `BookService` имеет прямую зависимость от `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="ddaf7-366">В соответствии с официальными [правилами повторного использования клиента Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)`MongoClient` следует регистрировать в DI с использованием времени существования отдельной службы.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="ddaf7-367">Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылку `BookService`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="ddaf7-368">Класс `BookService` использует следующие члены `MongoDB.Driver` для выполнения операций CRUD в базе данных:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="ddaf7-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): считывает экземпляр сервера для выполнения операций с базой данных.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="ddaf7-370">Конструктор этого класса предоставляет строку подключения MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="ddaf7-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): представляет базу данных Mongo для выполнения операций.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="ddaf7-372">В этом руководстве используется универсальный метод [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) интерфейса для получения доступа к данным в определенной коллекции.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="ddaf7-373">Операции CRUD следует выполнять с коллекцией после вызова этого метода.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="ddaf7-374">В вызове метода `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="ddaf7-375">`collection` представляет имя коллекции;</span><span class="sxs-lookup"><span data-stu-id="ddaf7-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="ddaf7-376">`TDocument` представляет тип объекта среды CLR, хранящегося в коллекции;</span><span class="sxs-lookup"><span data-stu-id="ddaf7-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="ddaf7-377">`GetCollection<TDocument>(collection)` возвращает объект [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm), представляющий коллекцию.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="ddaf7-378">В этом руководстве следующие методы вызываются для коллекции:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="ddaf7-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): удаляет один документ, отвечающий заданным критериям поиска.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="ddaf7-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): возвращает все документы в коллекции, соответствующие заданным критериям поиска.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="ddaf7-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): вставляет предоставленный объект в виде нового документа в коллекции.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="ddaf7-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): заменяет один документ, отвечающий заданным критериям поиска, предоставленным объектом.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="ddaf7-383">Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="ddaf7-383">Add a controller</span></span>

<span data-ttu-id="ddaf7-384">Добавьте класс `BooksController` в каталог *Controllers* с помощью такого кода:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="ddaf7-385">Предыдущий контроллер веб-API:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-385">The preceding web API controller:</span></span>

* <span data-ttu-id="ddaf7-386">использует класс `BookService` для выполнения операций CRUD;</span><span class="sxs-lookup"><span data-stu-id="ddaf7-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="ddaf7-387">содержит методы действий для поддержки запросов HTTP GET, POST, PUT и DELETE.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="ddaf7-388">Вызывает <xref:System.Web.Http.ApiController.CreatedAtRoute*> в методе действия `Create` для возврата ответа [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="ddaf7-389">Код состояния 201 представляет собой стандартный ответ метода HTTP POST, создающего ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="ddaf7-390">`CreatedAtRoute` также добавляет заголовок `Location` в ответ.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="ddaf7-391">Заголовок `Location` указывает универсальный код ресурса (URI) созданной книги.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="ddaf7-392">Тестирование веб-API</span><span class="sxs-lookup"><span data-stu-id="ddaf7-392">Test the web API</span></span>

1. <span data-ttu-id="ddaf7-393">Выполните сборку и запуск приложения.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-393">Build and run the app.</span></span>

1. <span data-ttu-id="ddaf7-394">Перейдите по адресу `http://localhost:<port>/api/books`, чтобы протестировать не имеющий параметров метод действия `Get` контроллера.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="ddaf7-395">Отобразится такой ответ JSON:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-395">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="ddaf7-396">Перейдите по адресу `http://localhost:<port>/api/books/{id here}`, чтобы протестировать перегруженный метод действия `Get` контроллера.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="ddaf7-397">Отобразится такой ответ JSON:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="ddaf7-398">Настройка параметров сериализации JSON</span><span class="sxs-lookup"><span data-stu-id="ddaf7-398">Configure JSON serialization options</span></span>

<span data-ttu-id="ddaf7-399">Нужно изменить два параметра для ответов JSON, возвращаемых в разделе [Тестирование веб-API](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="ddaf7-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="ddaf7-400">Смешанный регистр имен свойств по умолчанию следует изменить в соответствии с регистром Pascal имен свойств объекта CLR.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="ddaf7-401">Свойство `bookName` должно возвращаться как `Name`.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="ddaf7-402">Чтобы удовлетворить эти требования, внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="ddaf7-403">В `Startup.ConfigureServices` вставьте следующий выделенный код в вызов метода `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="ddaf7-404">После предыдущего изменения имена свойств в ответе сериализованного JSON веб-API соответствуют именам свойств в типе объекта CLR.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="ddaf7-405">Например, свойство `Author` класса `Book` сериализуется как `Author`.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="ddaf7-406">В *Models/Book.cs* добавьте к свойству `BookName` следующий атрибут [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm):</span><span class="sxs-lookup"><span data-stu-id="ddaf7-406">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="ddaf7-407">Значение атрибута `[JsonProperty]`, равное `Name`, представляет имя свойства в ответе сериализованного JSON веб-API.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="ddaf7-408">Добавьте следующий код в самое начало файла *Models/Book.cs*, чтобы разрешить ссылку на атрибут `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="ddaf7-409">Повторите действия, описанные в разделе [Тестирование веб-API](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="ddaf7-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="ddaf7-410">Обратите внимание на различие в именах свойств JSON.</span><span class="sxs-lookup"><span data-stu-id="ddaf7-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="ddaf7-411">Добавление поддержки аутентификации в веб-API</span><span class="sxs-lookup"><span data-stu-id="ddaf7-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="ddaf7-412">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="ddaf7-412">Next steps</span></span>

<span data-ttu-id="ddaf7-413">Дополнительные сведения о сборке веб-API ASP.NET Core см. в этих статьях:</span><span class="sxs-lookup"><span data-stu-id="ddaf7-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="ddaf7-414">Версия статьи на YouTube</span><span class="sxs-lookup"><span data-stu-id="ddaf7-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
