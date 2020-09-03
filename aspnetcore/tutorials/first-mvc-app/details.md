---
title: Часть 10. Изучение методов Details и Delete в приложении ASP.NET Core
author: rick-anderson
description: Часть 10 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 4683f106b3372a2f14f289b39c8a0b8b6c8b5aa1
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379371"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="63804-103">Часть 10. Изучение методов Details и Delete в приложении ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63804-103">Part 10, examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="63804-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="63804-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="63804-105">Откройте контроллер Movie и изучите метод `Details`:</span><span class="sxs-lookup"><span data-stu-id="63804-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="63804-106">Подсистема формирования шаблонов MVC, созданная этим методом действия, добавляет комментарий, показывающий HTTP-запрос, который вызывает метод.</span><span class="sxs-lookup"><span data-stu-id="63804-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="63804-107">Здесь это запрос GET, состоящий из трех сегментов URL-адреса, контроллера `Movies`, метода `Details` и значения `id`.</span><span class="sxs-lookup"><span data-stu-id="63804-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="63804-108">Эти сегменты определены в *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="63804-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="63804-109">EF упрощает поиск данных с помощью метода `FirstOrDefaultAsync`.</span><span class="sxs-lookup"><span data-stu-id="63804-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="63804-110">Важной функцией обеспечения безопасности, встроенной в метод, является то, что код проверяет, что метод поиска обнаружил фильм до выполнения с ним каких-либо действий.</span><span class="sxs-lookup"><span data-stu-id="63804-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="63804-111">Например, злоумышленник может внести ошибки на сайт путем изменения созданного ссылками URL-адреса с `http://localhost:{PORT}/Movies/Details/1` на что-то вроде `http://localhost:{PORT}/Movies/Details/12345` (или любое другое значение, которое не представляет фактический фильм).</span><span class="sxs-lookup"><span data-stu-id="63804-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:{PORT}/Movies/Details/1` to something like  `http://localhost:{PORT}/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="63804-112">Если вы не проверили наличие фильма со значением NULL, приложение выдаст исключение.</span><span class="sxs-lookup"><span data-stu-id="63804-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="63804-113">Просмотрите методы `Delete` и `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="63804-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="63804-114">Обратите внимание, что метод `HTTP GET Delete` не удаляет указанный фильм, он возвращает представление фильма, где можно выполнить (HttpPost) удаление.</span><span class="sxs-lookup"><span data-stu-id="63804-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="63804-115">Выполнение операции удаления в ответ на запрос GET (или выполнение операции редактирования, создания или любой другой операции, изменяющей данные) открывает брешь в системе безопасности.</span><span class="sxs-lookup"><span data-stu-id="63804-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="63804-116">Метод `[HttpPost]`, который удаляет данные, называется `DeleteConfirmed`, поэтому метод HTTP POST обладает уникальной сигнатурой или именем.</span><span class="sxs-lookup"><span data-stu-id="63804-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="63804-117">Ниже приведены сигнатуры двух методов:</span><span class="sxs-lookup"><span data-stu-id="63804-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="63804-118">Требуется, чтобы в среде CLR перегруженные методы имели уникальную сигнатуру параметров (то же имя метода, но другой список параметров).</span><span class="sxs-lookup"><span data-stu-id="63804-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="63804-119">Однако здесь необходимы два метода `Delete` — один для GET и один для POST — с одинаковой сигнатурой параметров.</span><span class="sxs-lookup"><span data-stu-id="63804-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="63804-120">(Они оба должны принимать целочисленное значение в качестве параметра.)</span><span class="sxs-lookup"><span data-stu-id="63804-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="63804-121">Существует два подхода к решению этой проблемы, один из которых заключается в указании разных имен для методов.</span><span class="sxs-lookup"><span data-stu-id="63804-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="63804-122">Именно это было представлено в предыдущем примере механизма формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="63804-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="63804-123">Но в этом случае возникает небольшая проблема: ASP.NET сопоставляет сегменты URL-адреса с методами действий по имени, а при переименовании метода, как правило, маршрутизация не сможет найти этот метод.</span><span class="sxs-lookup"><span data-stu-id="63804-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="63804-124">Решение показано в примере, а именно: в метод `DeleteConfirmed` следует добавить атрибут `ActionName("Delete")`.</span><span class="sxs-lookup"><span data-stu-id="63804-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="63804-125">Этот атрибут выполняет сопоставление для системы маршрутизации, чтобы URL-адрес, включающий /Delete/ для запроса POST, смог найти метод `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="63804-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="63804-126">Другим распространенным решением проблемы для методов с одинаковыми именами и сигнатурами является искусственное изменение сигнатуры метода POST для включения дополнительного (неиспользуемого) параметра.</span><span class="sxs-lookup"><span data-stu-id="63804-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="63804-127">Именно это было сделано ранее, когда был добавлен параметр `notUsed`.</span><span class="sxs-lookup"><span data-stu-id="63804-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="63804-128">То же самое можно сделать для метода `[HttpPost] Delete`:</span><span class="sxs-lookup"><span data-stu-id="63804-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="63804-129">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="63804-129">Publish to Azure</span></span>

<span data-ttu-id="63804-130">Сведения о развертывании в Azure, см. в разделе [Учебник: созданию приложения ASP.NET Core и Базы данных SQL в Службе приложений Azure](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span><span class="sxs-lookup"><span data-stu-id="63804-130">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core and SQL Database app in Azure App Service](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="63804-131">Назад</span><span class="sxs-lookup"><span data-stu-id="63804-131">Previous</span></span>](validation.md)
