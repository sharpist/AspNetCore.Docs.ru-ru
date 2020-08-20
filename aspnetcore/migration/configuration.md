---
title: Перенос конфигурации в ASP.NET Core
author: ardalis
description: Узнайте, как перенести конфигурацию из проекта ASP.NET MVC в проект ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
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
uid: migration/configuration
ms.openlocfilehash: 8cbb174539234039a05306ec5ab89026bc90da23
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631099"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="7f82b-103">Перенос конфигурации в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7f82b-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="7f82b-104">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith) и [Скотт Эдди](https://scottaddie.com) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="7f82b-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="7f82b-105">В предыдущей статье мы начали [Перенос проекта ASP.NET MVC на ASP.NET Core MVC](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="7f82b-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="7f82b-106">В этой статье мы выполним миграцию конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7f82b-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="7f82b-107">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7f82b-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="7f82b-108">Настройка конфигурации</span><span class="sxs-lookup"><span data-stu-id="7f82b-108">Setup configuration</span></span>

<span data-ttu-id="7f82b-109">ASP.NET Core больше не использует файлы *Global. asax* и *web.config* , которые использовались в предыдущих версиях ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7f82b-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="7f82b-110">В более ранних версиях ASP.NET логика запуска приложения была помещена в `Application_StartUp` метод в *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="7f82b-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="7f82b-111">Позже в ASP.NET MVC файл *Startup.CS* был добавлен в корень проекта. и был вызван при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="7f82b-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="7f82b-112">ASP.NET Core полностью приняла этот подход, поместив всю логику запуска в файл *Startup.CS* .</span><span class="sxs-lookup"><span data-stu-id="7f82b-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="7f82b-113">Файл *web.config* также был заменен в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f82b-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="7f82b-114">Теперь конфигурацию можно настроить в рамках процедуры запуска приложения, описанной в *Startup.CS*.</span><span class="sxs-lookup"><span data-stu-id="7f82b-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="7f82b-115">Конфигурация по-прежнему может использовать XML-файлы, но обычно ASP.NET Core проекты помещают значения конфигурации в файл в формате JSON, например *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="7f82b-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="7f82b-116">Система настройки ASP.NET Core также может легко получить доступ к переменным среды, что может обеспечить [более безопасное и надежное расположение](xref:security/app-secrets) для значений, зависящих от среды.</span><span class="sxs-lookup"><span data-stu-id="7f82b-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="7f82b-117">Это особенно справедливо для таких секретов, как строки подключения и ключи API, которые не должны быть возвращены в систему управления версиями.</span><span class="sxs-lookup"><span data-stu-id="7f82b-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="7f82b-118">Дополнительные сведения о настройке в ASP.NET Core см. в разделе [Конфигурация](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="7f82b-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="7f82b-119">В этой статье мы начинаем с частично перенесенного ASP.NET Core проекта из [предыдущей статьи](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="7f82b-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="7f82b-120">Чтобы настроить конфигурацию, добавьте следующий конструктор и свойство в файл *Startup.CS* , расположенный в корне проекта:</span><span class="sxs-lookup"><span data-stu-id="7f82b-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="7f82b-121">Обратите внимание, что на этом этапе файл *Startup.CS* не будет компилироваться, так как по-прежнему нужно добавить следующую `using` инструкцию:</span><span class="sxs-lookup"><span data-stu-id="7f82b-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="7f82b-122">Добавьте *appsettings.jsв* файл в корневую папку проекта, используя соответствующий шаблон элемента:</span><span class="sxs-lookup"><span data-stu-id="7f82b-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![Добавление формата AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="7f82b-124">Перенос параметров конфигурации из web.config</span><span class="sxs-lookup"><span data-stu-id="7f82b-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="7f82b-125">Наш проект ASP.NET MVC включал необходимую строку подключения к базе данных в *web.config*в `<connectionStrings>` элементе.</span><span class="sxs-lookup"><span data-stu-id="7f82b-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="7f82b-126">В нашем проекте ASP.NET Core эти сведения будут храниться в *appsettings.js* файле.</span><span class="sxs-lookup"><span data-stu-id="7f82b-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="7f82b-127">Откройте *appsettings.jsв*и обратите внимание, что в нем уже есть следующее:</span><span class="sxs-lookup"><span data-stu-id="7f82b-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="7f82b-128">В выделенной выше строке измените имя базы данных с **_CHANGE_ME** на имя базы данных.</span><span class="sxs-lookup"><span data-stu-id="7f82b-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="7f82b-129">Сводка</span><span class="sxs-lookup"><span data-stu-id="7f82b-129">Summary</span></span>

<span data-ttu-id="7f82b-130">ASP.NET Core помещает всю логику запуска приложения в один файл, в котором можно определить и настроить необходимые службы и зависимости.</span><span class="sxs-lookup"><span data-stu-id="7f82b-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="7f82b-131">Он заменяет файл *web.config* на гибкую функцию настройки, которая может использовать различные форматы файлов, такие как JSON, а также переменные среды.</span><span class="sxs-lookup"><span data-stu-id="7f82b-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
