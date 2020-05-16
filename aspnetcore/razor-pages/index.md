---
title: Введение в Razor Pages в ASP.NET Core
author: Rick-Anderson
description: Сведения о применении в ASP.NET Core функции Razor Pages, которая делает создание кодов сценариев для страниц проще и эффективнее по сравнению с MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 6939d285838a6dd971f530c1d65d73273b5b14e7
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424566"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="779a4-103">Введение в Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="779a4-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="779a4-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Райан Новак](https://github.com/rynowak) (Ryan Nowak)</span><span class="sxs-lookup"><span data-stu-id="779a4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="779a4-105">Razor Pages делает создание кодов сценариев для страниц проще и эффективнее по сравнению с использованием контроллеров и представлений.</span><span class="sxs-lookup"><span data-stu-id="779a4-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="779a4-106">Если вам нужно руководство, использующее подход "модель-представление-контроллер", см. статью [Начало работы с MVC в ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="779a4-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="779a4-107">Этот документ содержит вводные сведения о Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="779a4-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="779a4-108">Это не пошаговое руководство.</span><span class="sxs-lookup"><span data-stu-id="779a4-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="779a4-109">Если некоторые разделы покажутся вам слишком сложными, см. [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="779a4-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="779a4-110">Общие сведения об ASP.NET Core см. в разделе [Введение в ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="779a4-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="779a4-111">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="779a4-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="779a4-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="779a4-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="779a4-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="779a4-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="779a4-114">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="779a4-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="779a4-115">Создание проекта Razor Pages</span><span class="sxs-lookup"><span data-stu-id="779a4-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="779a4-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="779a4-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="779a4-117">Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="779a4-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="779a4-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="779a4-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="779a4-119">Из командной строки выполните команду `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="779a4-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="779a4-120">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="779a4-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="779a4-121">Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="779a4-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="779a4-122">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="779a4-122">Razor Pages</span></span>

<span data-ttu-id="779a4-123">Функция Razor Pages активируется в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="779a4-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="779a4-124">Рассмотрим простую страницу: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="779a4-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="779a4-125">Приведенный выше код выглядит как [файл представления Razor](xref:tutorials/first-mvc-app/adding-view), используемый в приложениях ASP.NET Core с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="779a4-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="779a4-126">Он отличается от него только директивой [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="779a4-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="779a4-127">Директива `@page` превращает файл в действие MVC, а значит обрабатывает запросы напрямую, минуя контроллер.</span><span class="sxs-lookup"><span data-stu-id="779a4-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="779a4-128">Директива `@page` должна быть первой директивой Razor на странице.</span><span class="sxs-lookup"><span data-stu-id="779a4-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="779a4-129">`@page` влияет на поведение всех остальных конструкций [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="779a4-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="779a4-130">Имена файлов Razor Pages имеют суффикс *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="779a4-131">Похожая страница с использованием класса `PageModel` показана в следующих двух файлах.</span><span class="sxs-lookup"><span data-stu-id="779a4-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="779a4-132">Файл *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="779a4-133">Модель страницы *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="779a4-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="779a4-134">Как правило, файл класса `PageModel` называется так же, как файл Razor Pages, но с расширением *.cs*.</span><span class="sxs-lookup"><span data-stu-id="779a4-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="779a4-135">Например, представленная выше страница Razor Pages называется *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="779a4-136">Файл, содержащий класс `PageModel`, называется *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="779a4-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="779a4-137">Сопоставления URL-адресов со страницами определяются расположением конкретной страницы в файловой системе.</span><span class="sxs-lookup"><span data-stu-id="779a4-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="779a4-138">В приведенной ниже таблице показаны пути Razor Pages и соответствующие URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="779a4-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="779a4-139">Имя файла и путь</span><span class="sxs-lookup"><span data-stu-id="779a4-139">File name and path</span></span>               | <span data-ttu-id="779a4-140">Соответствующий URL</span><span class="sxs-lookup"><span data-stu-id="779a4-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="779a4-141">*/Pages/index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="779a4-142">`/` или `/Index`</span><span class="sxs-lookup"><span data-stu-id="779a4-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="779a4-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="779a4-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="779a4-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="779a4-146">`/Store` или `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="779a4-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="779a4-147">Примечания.</span><span class="sxs-lookup"><span data-stu-id="779a4-147">Notes:</span></span>

* <span data-ttu-id="779a4-148">Среда выполнения по умолчанию ищет файлы Razor Pages в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="779a4-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="779a4-149">Если в URL-адресе не указана конкретная страница, по умолчанию открывается страница `Index`.</span><span class="sxs-lookup"><span data-stu-id="779a4-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="779a4-150">Создание простой формы</span><span class="sxs-lookup"><span data-stu-id="779a4-150">Write a basic form</span></span>

<span data-ttu-id="779a4-151">Функция Razor Pages предназначена для упрощения реализации типовых шаблонов, которые используются в браузерах, при создании приложения.</span><span class="sxs-lookup"><span data-stu-id="779a4-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="779a4-152">[Привязки модели](xref:mvc/models/model-binding), [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и вспомогательные методы HTML *отлично работают* со свойствами, определенными в классе Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="779a4-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="779a4-153">Рассмотрим страницу с простой формой связи для модели `Contact`.</span><span class="sxs-lookup"><span data-stu-id="779a4-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="779a4-154">В представленных в этой статье примерах `DbContext` инициализируется в файле [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="779a4-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="779a4-155">Модель данных:</span><span class="sxs-lookup"><span data-stu-id="779a4-155">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="779a4-156">Контекст базы данных:</span><span class="sxs-lookup"><span data-stu-id="779a4-156">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="779a4-157">Файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="779a4-158">Модель страницы *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="779a4-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="779a4-159">Как правило, класс `PageModel` называется `<PageName>Model` и находится в том же пространстве имен, что и страница.</span><span class="sxs-lookup"><span data-stu-id="779a4-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="779a4-160">Класс `PageModel` позволяет разделять логику страницы и ее представление.</span><span class="sxs-lookup"><span data-stu-id="779a4-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="779a4-161">Он определяет обработчики страницы для запросов, отправляемых на страницу, а также данные для ее визуализации.</span><span class="sxs-lookup"><span data-stu-id="779a4-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="779a4-162">Это разделение позволяет:</span><span class="sxs-lookup"><span data-stu-id="779a4-162">This separation allows:</span></span>

* <span data-ttu-id="779a4-163">управлять зависимостями страниц с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="779a4-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="779a4-164">Модульное тестирование</span><span class="sxs-lookup"><span data-stu-id="779a4-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="779a4-165">Страница содержит *метод обработчика* `OnPostAsync`, который выполняется по запросам `POST` (когда пользователь публикует форму).</span><span class="sxs-lookup"><span data-stu-id="779a4-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="779a4-166">Можно добавить методы обработчика для любой HTTP-команды.</span><span class="sxs-lookup"><span data-stu-id="779a4-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="779a4-167">Наиболее распространенные обработчики</span><span class="sxs-lookup"><span data-stu-id="779a4-167">The most common handlers are:</span></span>

* <span data-ttu-id="779a4-168">`OnGet` — инициализация необходимого для страницы состояния.</span><span class="sxs-lookup"><span data-stu-id="779a4-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="779a4-169">В приведенном выше коде метод `OnGet` отображает страницу Razor *CreateModel.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="779a4-170">`OnPost` — обработка отправленных через форму данных.</span><span class="sxs-lookup"><span data-stu-id="779a4-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="779a4-171">Суффикс `Async` не является обязательным, но часто используется для асинхронных функций.</span><span class="sxs-lookup"><span data-stu-id="779a4-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="779a4-172">Этот код типичен для Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="779a4-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="779a4-173">Если вы знакомы с приложениями ASP.NET, использующими контроллеры и представления:</span><span class="sxs-lookup"><span data-stu-id="779a4-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="779a4-174">Код `OnPostAsync` в предыдущем примере похож на стандартный код контроллера.</span><span class="sxs-lookup"><span data-stu-id="779a4-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="779a4-175">Большинство примитивов MVC, включая [привязку модели](xref:mvc/models/model-binding), [проверку](xref:mvc/models/validation) и результаты действий, одинаково работают с контроллерами и Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="779a4-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="779a4-176">Предыдущий метод `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="779a4-176">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="779a4-177">Простая схема `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="779a4-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="779a4-178">Проверка на наличие ошибок проверки.</span><span class="sxs-lookup"><span data-stu-id="779a4-178">Check for validation errors.</span></span>

* <span data-ttu-id="779a4-179">Если ошибок нет, сохранение данных и перенаправление.</span><span class="sxs-lookup"><span data-stu-id="779a4-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="779a4-180">Если есть ошибки, отображение страницы с сообщениями проверки.</span><span class="sxs-lookup"><span data-stu-id="779a4-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="779a4-181">Во многих случаях ошибки проверки выявляются на клиентском компьютере и на сервер не отправляются.</span><span class="sxs-lookup"><span data-stu-id="779a4-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="779a4-182">Файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="779a4-183">Преобразованный HTML из *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="779a4-184">В приведенном выше коде, разместив форму:</span><span class="sxs-lookup"><span data-stu-id="779a4-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="779a4-185">С допустимыми данными:</span><span class="sxs-lookup"><span data-stu-id="779a4-185">With valid data:</span></span>

  * <span data-ttu-id="779a4-186">Метод обработчика `OnPostAsync` вызывает вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>.</span><span class="sxs-lookup"><span data-stu-id="779a4-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="779a4-187">`RedirectToPage` возвращает экземпляр <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="779a4-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="779a4-188">`RedirectToPage`.</span><span class="sxs-lookup"><span data-stu-id="779a4-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="779a4-189">Является результатом действия.</span><span class="sxs-lookup"><span data-stu-id="779a4-189">Is an action result.</span></span>
    * <span data-ttu-id="779a4-190">Аналогичен `RedirectToAction` или `RedirectToRoute` (используется в контроллерах и представлениях).</span><span class="sxs-lookup"><span data-stu-id="779a4-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="779a4-191">Настраивается для страниц.</span><span class="sxs-lookup"><span data-stu-id="779a4-191">Is customized for pages.</span></span> <span data-ttu-id="779a4-192">В приведенном выше примере он выполняет перенаправление на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="779a4-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="779a4-193">Более подробно `RedirectToPage` рассматривается в разделе [Создание URL для страниц](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="779a4-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="779a4-194">С ошибками проверки, передаваемыми на сервер:</span><span class="sxs-lookup"><span data-stu-id="779a4-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="779a4-195">Метод обработчика `OnPostAsync` вызывает вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>.</span><span class="sxs-lookup"><span data-stu-id="779a4-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="779a4-196">`Page` возвращает экземпляр <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="779a4-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="779a4-197">Возвращение `Page` аналогично тому, как действия в контроллерах возвращают `View`.</span><span class="sxs-lookup"><span data-stu-id="779a4-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="779a4-198">`PageResult` — тип возвращаемого значения по умолчанию для метода обработчика.</span><span class="sxs-lookup"><span data-stu-id="779a4-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="779a4-199">Метод обработчика, вернувший `void`, визуализирует страницу.</span><span class="sxs-lookup"><span data-stu-id="779a4-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="779a4-200">В предыдущем примере публикация формы без значения приводит к тому, что [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) возвращает значение false.</span><span class="sxs-lookup"><span data-stu-id="779a4-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="779a4-201">В этом примере на клиенте не отображаются ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="779a4-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="779a4-202">Обработка ошибок проверки рассматривается далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="779a4-202">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="779a4-203">С ошибками проверки, обнаруженными при проверке на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="779a4-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="779a4-204">Данные **не** отправляются на сервер.</span><span class="sxs-lookup"><span data-stu-id="779a4-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="779a4-205">Проверка на стороне клиента описана далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="779a4-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="779a4-206">Для указания согласия на привязку модели в свойстве `Customer` используется атрибут [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute):</span><span class="sxs-lookup"><span data-stu-id="779a4-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="779a4-207">`[BindProperty]`**не** следует использовать в моделях, содержащих свойства, которые не должны изменяться клиентом.</span><span class="sxs-lookup"><span data-stu-id="779a4-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="779a4-208">Дополнительные сведения см. в [этом разделе](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="779a4-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="779a4-209">По умолчанию функция Razor Pages привязывает свойства ко всем командам, кроме `GET`.</span><span class="sxs-lookup"><span data-stu-id="779a4-209">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="779a4-210">Привязка к свойствам устраняет необходимость в написании кода для преобразования данных HTTP в тип модели.</span><span class="sxs-lookup"><span data-stu-id="779a4-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="779a4-211">Привязка уменьшает код за счет того, что для визуализации полей формы (`<input asp-for="Customer.Name">`) и получения входных данных используется одно и то же свойство.</span><span class="sxs-lookup"><span data-stu-id="779a4-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="779a4-212">Просмотр файла представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="779a4-213">В приведенном выше коде [вспомогательная функция тега ввода](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` привязывает элемент `<input>` HTML к выражению модели `Customer.Name`.</span><span class="sxs-lookup"><span data-stu-id="779a4-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="779a4-214">Директива [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) обеспечивает доступность вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="779a4-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="779a4-215">Домашняя страница</span><span class="sxs-lookup"><span data-stu-id="779a4-215">The home page</span></span>

<span data-ttu-id="779a4-216">*Index.cshtml* — это домашняя страница:</span><span class="sxs-lookup"><span data-stu-id="779a4-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="779a4-217">Связанный класс `PageModel` (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="779a4-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="779a4-218">Файл *index.cshtml* содержит следующую разметку:</span><span class="sxs-lookup"><span data-stu-id="779a4-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="779a4-219">[Вспомогательный тег привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a /a>` использовал атрибут `asp-route-{value}` для создания ссылки на страницу редактирования.</span><span class="sxs-lookup"><span data-stu-id="779a4-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="779a4-220">Эта ссылка содержит данные о маршруте с идентификатором контактного лица.</span><span class="sxs-lookup"><span data-stu-id="779a4-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="779a4-221">Например, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="779a4-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="779a4-222">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="779a4-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="779a4-223">Файл *Index.cshtml* содержит разметку для создания кнопки удаления у каждого контакта клиента:</span><span class="sxs-lookup"><span data-stu-id="779a4-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="779a4-224">Отображаемый HTML:</span><span class="sxs-lookup"><span data-stu-id="779a4-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="779a4-225">Во время обработки кнопки удаления в HTML ее [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) включает параметры для следующего:</span><span class="sxs-lookup"><span data-stu-id="779a4-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="779a4-226">Идентификатор контакта клиента, указанный атрибутом `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="779a4-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="779a4-227">Параметр `handler`, указанный атрибутом `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="779a4-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="779a4-228">При выборе кнопки на сервер отправляется запрос формы `POST`.</span><span class="sxs-lookup"><span data-stu-id="779a4-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="779a4-229">По соглашению имя метода обработчика выбирается на основе значения параметра `handler` в соответствии со схемой `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="779a4-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="779a4-230">Так как `handler` — `delete` в этом примере, метод обработчика `OnPostDeleteAsync` используется для обработки запроса `POST`.</span><span class="sxs-lookup"><span data-stu-id="779a4-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="779a4-231">Если `asp-page-handler` имеет другое значение, например `remove`, выбирается метод обработчика с именем `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="779a4-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="779a4-232">Метод `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="779a4-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="779a4-233">Получает `id` из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="779a4-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="779a4-234">Отправляет в базу данных запрос контакта клиента с `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="779a4-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="779a4-235">Если контакт клиента найден, он удаляется, и база данных обновляется.</span><span class="sxs-lookup"><span data-stu-id="779a4-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="779a4-236">Вызывает <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> для перенаправления на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="779a4-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="779a4-237">Файл Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="779a4-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="779a4-238">Первая строка содержит директиву `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="779a4-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="779a4-239">Ограничение маршрутизации `"{id:int}"` указывает, что страница должна принимать обращенные к ней запросы, которые содержат данные о маршруте `int`.</span><span class="sxs-lookup"><span data-stu-id="779a4-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="779a4-240">Если запрос к странице не содержит данные о маршруте, которые можно конвертировать в `int`, среда выполнения возвращает ошибку HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="779a4-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="779a4-241">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="779a4-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="779a4-242">Файл *Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="779a4-242">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="779a4-243">Проверка</span><span class="sxs-lookup"><span data-stu-id="779a4-243">Validation</span></span>

<span data-ttu-id="779a4-244">Правила проверки:</span><span class="sxs-lookup"><span data-stu-id="779a4-244">Validation rules:</span></span>

* <span data-ttu-id="779a4-245">Декларативно задаются в классе Model.</span><span class="sxs-lookup"><span data-stu-id="779a4-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="779a4-246">Применяются везде в приложении.</span><span class="sxs-lookup"><span data-stu-id="779a4-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="779a4-247">Пространство имен <xref:System.ComponentModel.DataAnnotations> предоставляет набор встроенных атрибутов проверки, которые декларативно применяются к классу или свойству.</span><span class="sxs-lookup"><span data-stu-id="779a4-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="779a4-248">Кроме того, DataAnnotations содержит атрибуты форматирования (такие как [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)), которые обеспечивают форматирование и не предназначены для проверки.</span><span class="sxs-lookup"><span data-stu-id="779a4-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="779a4-249">Рассмотрим модель `Customer`:</span><span class="sxs-lookup"><span data-stu-id="779a4-249">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="779a4-250">Используя следующий файл представления *Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="779a4-251">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="779a4-251">The preceding code:</span></span>

* <span data-ttu-id="779a4-252">Включает jQuery и скрипты проверки jQuery.</span><span class="sxs-lookup"><span data-stu-id="779a4-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="779a4-253">Использует [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) `<div />` и `<span />` для следующих задач:</span><span class="sxs-lookup"><span data-stu-id="779a4-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="779a4-254">Проверка на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="779a4-254">Client-side validation.</span></span>
  * <span data-ttu-id="779a4-255">Отображение ошибок при проверке.</span><span class="sxs-lookup"><span data-stu-id="779a4-255">Validation error rendering.</span></span>

* <span data-ttu-id="779a4-256">Генерирует следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="779a4-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="779a4-257">При публикации формы создания без значения имени в форме отображается сообщение об ошибке: "Поле имени является обязательным".</span><span class="sxs-lookup"><span data-stu-id="779a4-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="779a4-258">в форме.</span><span class="sxs-lookup"><span data-stu-id="779a4-258">on the form.</span></span> <span data-ttu-id="779a4-259">Если на клиенте включен JavaScript, браузер отображает ошибку без отправки на сервер.</span><span class="sxs-lookup"><span data-stu-id="779a4-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="779a4-260">Атрибут `[StringLength(10)]` создает `data-val-length-max="10"` в отображаемом HTML-коде.</span><span class="sxs-lookup"><span data-stu-id="779a4-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="779a4-261">`data-val-length-max` не дает браузерам ввести больше заданной максимальной длины.</span><span class="sxs-lookup"><span data-stu-id="779a4-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="779a4-262">Если для изменения и воспроизведения записи используется средство, например [Fiddler](https://www.telerik.com/fiddler), выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="779a4-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="779a4-263">С именем, превышающим 10.</span><span class="sxs-lookup"><span data-stu-id="779a4-263">With the name longer than 10.</span></span>
* <span data-ttu-id="779a4-264">Возвращается сообщение об ошибке: "Имя поля должно быть строкой с максимальной длиной 10".</span><span class="sxs-lookup"><span data-stu-id="779a4-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="779a4-265">.</span><span class="sxs-lookup"><span data-stu-id="779a4-265">is returned.</span></span>

<span data-ttu-id="779a4-266">Рассмотрим следующую модель `Movie`:</span><span class="sxs-lookup"><span data-stu-id="779a4-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="779a4-267">Атрибуты проверки определяют поведение для свойств модели, к которым они применяются:</span><span class="sxs-lookup"><span data-stu-id="779a4-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="779a4-268">Атрибуты `Required` и `MinimumLength` указывают, что свойство должно иметь значение. Тем не менее, чтобы удовлетворить требованиям проверки, пользователю достаточно ввести пробел.</span><span class="sxs-lookup"><span data-stu-id="779a4-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="779a4-269">Атрибут `RegularExpression` ограничивает набор допустимых для ввода символов.</span><span class="sxs-lookup"><span data-stu-id="779a4-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="779a4-270">В приведенном выше коде в Genre:</span><span class="sxs-lookup"><span data-stu-id="779a4-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="779a4-271">должны использоваться только буквы;</span><span class="sxs-lookup"><span data-stu-id="779a4-271">Must only use letters.</span></span>
  * <span data-ttu-id="779a4-272">первая буква должна быть прописной;</span><span class="sxs-lookup"><span data-stu-id="779a4-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="779a4-273">пробелы, цифры и специальные символы не допускаются.</span><span class="sxs-lookup"><span data-stu-id="779a4-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="779a4-274">В `RegularExpression` Rating:</span><span class="sxs-lookup"><span data-stu-id="779a4-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="779a4-275">первый символ должен быть прописной буквой;</span><span class="sxs-lookup"><span data-stu-id="779a4-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="779a4-276">допускаются специальные символы и цифры, а также последующие пробелы.</span><span class="sxs-lookup"><span data-stu-id="779a4-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="779a4-277">Значение "PG-13" допустимо для рейтинга, но недопустимо для жанра.</span><span class="sxs-lookup"><span data-stu-id="779a4-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="779a4-278">Атрибут `Range` ограничивает диапазон значений.</span><span class="sxs-lookup"><span data-stu-id="779a4-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="779a4-279">Атрибут `StringLength` задает максимальную и при необходимости минимальную длину строкового свойства.</span><span class="sxs-lookup"><span data-stu-id="779a4-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="779a4-280">Типы значений (например, `decimal`, `int`, `float`, `DateTime`) по своей природе являются обязательными и не требуют атрибута `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="779a4-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="779a4-281">На странице создания для модели `Movie` отображаются ошибки с недопустимыми значениями:</span><span class="sxs-lookup"><span data-stu-id="779a4-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Форма просмотра фильма с несколькими ошибками проверки jQuery на стороне клиента](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="779a4-283">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="779a4-283">For more information, see:</span></span>

* [<span data-ttu-id="779a4-284">Добавление проверки в приложение Movie</span><span class="sxs-lookup"><span data-stu-id="779a4-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="779a4-285">[Проверка модели в ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="779a4-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="779a4-286">Обработка запросов HEAD с помощью вызова резервного обработчика OnGet</span><span class="sxs-lookup"><span data-stu-id="779a4-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="779a4-287">Запросы `HEAD` позволяют получать заголовки для определенного ресурса.</span><span class="sxs-lookup"><span data-stu-id="779a4-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="779a4-288">В отличие от запросов `GET` запросы `HEAD`не возвращают текст ответа.</span><span class="sxs-lookup"><span data-stu-id="779a4-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="779a4-289">Обработчик `OnHead` обычно создается и вызывается для выполнения запросов `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="779a4-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="779a4-290">Если обработчик `OnHead` не определен, Razor Pages выполнит вызов обработчика `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="779a4-290">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="779a4-291">XSRF/CSRF и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="779a4-291">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="779a4-292">В Razor Pages реализована [проверка для защиты от подделки](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="779a4-292">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="779a4-293">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) вставляет маркеры защиты от подделки в элементы HTML-форм.</span><span class="sxs-lookup"><span data-stu-id="779a4-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="779a4-294">Использование макетов, частичных реплик, шаблонов и вспомогательных функций тегов с Razor Pages</span><span class="sxs-lookup"><span data-stu-id="779a4-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="779a4-295">Pages работает со всеми функциями подсистемы просмотра Razor.</span><span class="sxs-lookup"><span data-stu-id="779a4-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="779a4-296">Макеты, частичные реплики, шаблоны, вспомогательные функции тегов, а также файлы *_ViewStart.cshtml* и *_ViewImports.cshtml* работают точно так же, как и в стандартных представлениях Razor.</span><span class="sxs-lookup"><span data-stu-id="779a4-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="779a4-297">Давайте упростим нашу страницу с помощью некоторых из этих функций.</span><span class="sxs-lookup"><span data-stu-id="779a4-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="779a4-298">Добавим [макет страницы](xref:mvc/views/layout) в файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="779a4-299">Этот [макет](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="779a4-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="779a4-300">управляет макетом каждой страницы (кроме страниц с отказом от макета);</span><span class="sxs-lookup"><span data-stu-id="779a4-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="779a4-301">импортирует HTML-структуры, такие как JavaScript и таблицы стилей.</span><span class="sxs-lookup"><span data-stu-id="779a4-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="779a4-302">Содержимое страницы Razor отображается в том месте, где вызывается `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="779a4-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="779a4-303">Дополнительные сведения см. [здесь](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="779a4-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="779a4-304">Свойство [Layout](xref:mvc/views/layout#specifying-a-layout) определяется в файле *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="779a4-305">Макет хранится в папке *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="779a4-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="779a4-306">Pages ищет другие представления (макеты, шаблоны, частичные реплики) в иерархическом порядке, начиная с той папки, где находится текущая страница.</span><span class="sxs-lookup"><span data-stu-id="779a4-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="779a4-307">Макет в папке *Pages/Shared* можно использовать на любой странице Razor, которая находится в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="779a4-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="779a4-308">Файл макета следует поместить в папку *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="779a4-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="779a4-309">Корпорация Майкрософт рекомендует **не** размещать файл макета в папке *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="779a4-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="779a4-310">*Views/Shared* — это шаблон представлений MVC.</span><span class="sxs-lookup"><span data-stu-id="779a4-310">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="779a4-311">Razor Pages опирается на иерархию папок, а не на условные обозначения путей.</span><span class="sxs-lookup"><span data-stu-id="779a4-311">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="779a4-312">Поиск представлений в Razor Pages охватывает папку *Pages*.</span><span class="sxs-lookup"><span data-stu-id="779a4-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="779a4-313">Макеты, шаблоны и частичные реплики *работают* с контроллерами MVC и стандартными представлениями Razor.</span><span class="sxs-lookup"><span data-stu-id="779a4-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="779a4-314">Добавим файл *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="779a4-315">`@namespace` описывается далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="779a4-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="779a4-316">Директива `@addTagHelper` добавляет [встроенные вспомогательные теги](xref:mvc/views/tag-helpers/builtin-th/Index) на все страницы в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="779a4-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="779a4-317">Директива `@namespace`, заданная на странице:</span><span class="sxs-lookup"><span data-stu-id="779a4-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="779a4-318">Директива `@namespace` задает пространство имен для страницы.</span><span class="sxs-lookup"><span data-stu-id="779a4-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="779a4-319">Включать пространство имен в директиву `@model` не требуется.</span><span class="sxs-lookup"><span data-stu-id="779a4-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="779a4-320">Если директива `@namespace` содержится в файле *_ViewImports.cshtml*, указанное пространство имен определяет префикс для созданного в Pages пространства имен, куда импортируется директива `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="779a4-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="779a4-321">Остальная часть созданного пространства имен (суффикс) представляет собой разделенный точками относительный путь между папкой с файлом *_ViewImports.cshtml* и папкой, содержащей страницу.</span><span class="sxs-lookup"><span data-stu-id="779a4-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="779a4-322">Например, класс `PageModel` в файле *Pages/Customers/Edit.cshtml.cs* задает пространство имен явно.</span><span class="sxs-lookup"><span data-stu-id="779a4-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="779a4-323">Файл *Pages/_ViewImports.cshtml* задает следующее пространство имен:</span><span class="sxs-lookup"><span data-stu-id="779a4-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="779a4-324">Сформированное пространство имен для файла *Pages/Customers/Edit.cshtml* Razor Pages совпадает с пространством имен класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="779a4-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="779a4-325">`@namespace` *также работает со стандартными представлениями Razor.*</span><span class="sxs-lookup"><span data-stu-id="779a4-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="779a4-326">Рассмотрим файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="779a4-327">Обновленный файл представления *Pages/Create.cshtml* с *_ViewImports.cshtml* и предыдущим файлом макета:</span><span class="sxs-lookup"><span data-stu-id="779a4-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="779a4-328">В приведенном выше коде *_ViewImports. cshtml* импортировал пространство имен и вспомогательные функции тегов.</span><span class="sxs-lookup"><span data-stu-id="779a4-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="779a4-329">Файл макета импортировал файлы JavaScript.</span><span class="sxs-lookup"><span data-stu-id="779a4-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="779a4-330">[Начальный проект Razor Pages](#rpvs17) содержит файл *Pages/_ValidationScriptsPartial.cshtml*, который подключает проверку на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="779a4-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="779a4-331">Дополнительные сведения о частичных представлениях см. в <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="779a4-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="779a4-332">Формирование URL-адресов для страниц</span><span class="sxs-lookup"><span data-stu-id="779a4-332">URL generation for Pages</span></span>

<span data-ttu-id="779a4-333">На представленной выше странице `Create` используется `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="779a4-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="779a4-334">Это приложение имеет следующую структуру файлов и папок:</span><span class="sxs-lookup"><span data-stu-id="779a4-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="779a4-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="779a4-335">*/Pages*</span></span>

  * <span data-ttu-id="779a4-336">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="779a4-337">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="779a4-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="779a4-338">*/Customers*</span></span>

    * <span data-ttu-id="779a4-339">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="779a4-340">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="779a4-341">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-341">*Index.cshtml*</span></span>

<span data-ttu-id="779a4-342">После успешного выполнения страницы *Pages/Customers/Create.cshtml* и *Pages/Customers/Edit.cshtml* перенаправляются на страницу *Pages/Customers/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="779a4-343">Строка `./Index` — это относительное имя страницы, используемое для доступа к предыдущей странице.</span><span class="sxs-lookup"><span data-stu-id="779a4-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="779a4-344">Эта строка позволяет создавать URL-адреса для страницы *Pages/Customers/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="779a4-345">Пример:</span><span class="sxs-lookup"><span data-stu-id="779a4-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="779a4-346">С помощью абсолютного имени страницы `/Index` создаются URL-адреса страницы *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="779a4-347">Пример:</span><span class="sxs-lookup"><span data-stu-id="779a4-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="779a4-348">Имя страницы — это путь к странице из корневой папки */Pages*, включая начальный символ `/` (например, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="779a4-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="779a4-349">Предыдущие образцы создания URL-адреса обеспечивают расширенные параметры и функциональные возможности по сравнению с жестким заданием URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="779a4-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="779a4-350">Формирование URL-адресов включает [маршрутизацию](xref:mvc/controllers/routing) и позволяет генерировать и включать в код параметры в зависимости от того, как определяется маршрут в пути назначения.</span><span class="sxs-lookup"><span data-stu-id="779a4-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="779a4-351">Формирование URL-адресов для страниц поддерживает относительные имена.</span><span class="sxs-lookup"><span data-stu-id="779a4-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="779a4-352">В приведенной ниже таблице показано, какая страница индекса выбирается с помощью разных параметров `RedirectToPage` в *Pages/Customers/Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="779a4-353">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="779a4-353">RedirectToPage(x)</span></span>| <span data-ttu-id="779a4-354">Страница</span><span class="sxs-lookup"><span data-stu-id="779a4-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="779a4-355">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="779a4-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="779a4-356">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="779a4-356">*Pages/Index*</span></span> |
| <span data-ttu-id="779a4-357">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="779a4-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="779a4-358">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="779a4-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="779a4-359">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="779a4-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="779a4-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="779a4-360">*Pages/Index*</span></span> |
| <span data-ttu-id="779a4-361">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="779a4-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="779a4-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="779a4-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="779a4-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")` и `RedirectToPage("../Index")` — это *относительные имена*.</span><span class="sxs-lookup"><span data-stu-id="779a4-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="779a4-364">Для получения имени целевой страницы параметр `RedirectToPage`*комбинируется* с путем текущей страницы.</span><span class="sxs-lookup"><span data-stu-id="779a4-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="779a4-365">Привязка относительных имен полезна при создании сайтов со сложной структурой.</span><span class="sxs-lookup"><span data-stu-id="779a4-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="779a4-366">Если относительные имена используются для связи между страницами в папке:</span><span class="sxs-lookup"><span data-stu-id="779a4-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="779a4-367">Переименование папки не нарушает относительные ссылки.</span><span class="sxs-lookup"><span data-stu-id="779a4-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="779a4-368">Ссылки не нарушаются, так как не содержат имя папки.</span><span class="sxs-lookup"><span data-stu-id="779a4-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="779a4-369">Чтобы выполнить перенаправление на страницу в другой [области](xref:mvc/controllers/areas), укажите эту область:</span><span class="sxs-lookup"><span data-stu-id="779a4-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="779a4-370">Дополнительные сведения см. в разделах <xref:mvc/controllers/areas> и <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="779a4-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="779a4-371">Атрибут ViewData</span><span class="sxs-lookup"><span data-stu-id="779a4-371">ViewData attribute</span></span>

<span data-ttu-id="779a4-372">Данные могут передаваться на страницу с помощью атрибута <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span><span class="sxs-lookup"><span data-stu-id="779a4-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="779a4-373">Значения свойств с атрибутом `[ViewData]` хранятся в <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> и загружаются из него.</span><span class="sxs-lookup"><span data-stu-id="779a4-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="779a4-374">В следующем примере `AboutModel` применяет атрибут `[ViewData]` к свойству `Title`:</span><span class="sxs-lookup"><span data-stu-id="779a4-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="779a4-375">На странице About доступ к свойству `Title` осуществляется как доступ к свойству модели.</span><span class="sxs-lookup"><span data-stu-id="779a4-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="779a4-376">В макете заголовок считывается из словаря ViewData.</span><span class="sxs-lookup"><span data-stu-id="779a4-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="779a4-377">TempData</span><span class="sxs-lookup"><span data-stu-id="779a4-377">TempData</span></span>

<span data-ttu-id="779a4-378">ASP.NET Core предоставляет <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span><span class="sxs-lookup"><span data-stu-id="779a4-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="779a4-379">Это свойство хранит данные до тех пор, пока они не будут прочитаны.</span><span class="sxs-lookup"><span data-stu-id="779a4-379">This property stores data until it's read.</span></span> <span data-ttu-id="779a4-380">Для проверки данных без удаления можно использовать методы <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> и <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*>.</span><span class="sxs-lookup"><span data-stu-id="779a4-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="779a4-381">`TempData` удобно использовать для перенаправления, когда данные требуются больше, чем для одного запроса.</span><span class="sxs-lookup"><span data-stu-id="779a4-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="779a4-382">В следующем коде значение `Message` задается с помощью `TempData`:</span><span class="sxs-lookup"><span data-stu-id="779a4-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="779a4-383">Представленная ниже разметка в файле *Pages/Customers/Index.cshtml* отображает значение `Message` с помощью `TempData`.</span><span class="sxs-lookup"><span data-stu-id="779a4-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="779a4-384">Модель страницы *Pages/Customers/Index.cshtml.cs* применяет атрибут `[TempData]` к свойству `Message`.</span><span class="sxs-lookup"><span data-stu-id="779a4-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="779a4-385">Дополнительные сведения см. в разделе [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="779a4-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="779a4-386">Несколько обработчиков на страницу</span><span class="sxs-lookup"><span data-stu-id="779a4-386">Multiple handlers per page</span></span>

<span data-ttu-id="779a4-387">Следующая страница формирует разметку для двух обработчиков с помощью вспомогательной функции тегов `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="779a4-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="779a4-388">Форма в предыдущем примере включает две кнопки отправки, каждая из которых отправляет данные на отдельный URL-адрес с помощью `FormActionTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="779a4-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="779a4-389">Атрибут `asp-page-handler` является дополнением к `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="779a4-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="779a4-390">Атрибут `asp-page-handler` формирует URL-адреса, ,которые используются для отправки данных в каждый из методов обработчиков, определенных страницей.</span><span class="sxs-lookup"><span data-stu-id="779a4-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="779a4-391">`asp-page` не задается, так как пример сопоставлен с текущей страницей.</span><span class="sxs-lookup"><span data-stu-id="779a4-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="779a4-392">Модель страницы</span><span class="sxs-lookup"><span data-stu-id="779a4-392">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="779a4-393">В представленном выше коде используются *именованные методы обработчика*.</span><span class="sxs-lookup"><span data-stu-id="779a4-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="779a4-394">Именованные методы обработчика создаются путем размещения определенного текста в имени после `On<HTTP Verb>` и перед `Async` (если есть).</span><span class="sxs-lookup"><span data-stu-id="779a4-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="779a4-395">В приведенном выше примере использовались методы страницы OnPost**JoinList**Async и OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="779a4-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="779a4-396">Если убрать *OnPost* и *Async*, имена обработчиков будут выглядеть как `JoinList` и `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="779a4-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="779a4-397">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="779a4-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="779a4-398">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="779a4-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="779a4-399">Пользовательские маршруты</span><span class="sxs-lookup"><span data-stu-id="779a4-399">Custom routes</span></span>

<span data-ttu-id="779a4-400">С помощью директивы `@page` можно сделать следующее.</span><span class="sxs-lookup"><span data-stu-id="779a4-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="779a4-401">Указать пользовательский маршрут к странице.</span><span class="sxs-lookup"><span data-stu-id="779a4-401">Specify a custom route to a page.</span></span> <span data-ttu-id="779a4-402">Например, можно задать маршрут к странице "Сведения" `/Some/Other/Path`: `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="779a4-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="779a4-403">Добавить сегменты к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="779a4-403">Append segments to a page's default route.</span></span> <span data-ttu-id="779a4-404">Например, к такому маршруту можно добавить сегмент item: `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="779a4-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="779a4-405">Добавить параметры к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="779a4-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="779a4-406">Например, для страницы с `@page "{id}"` может потребоваться параметр идентификатора `id`.</span><span class="sxs-lookup"><span data-stu-id="779a4-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="779a4-407">Поддерживается путь относительно корня, заданный знаком тильды (`~`) в начале пути.</span><span class="sxs-lookup"><span data-stu-id="779a4-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="779a4-408">Например, `@page "~/Some/Other/Path"` равносильно `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="779a4-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="779a4-409">Если вы не хотите, чтобы в URL-адресе отображалась строка запроса `?handler=JoinList`, измените маршрут так, чтобы в качестве пути в URL-адресе указывалось имя обработчика.</span><span class="sxs-lookup"><span data-stu-id="779a4-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="779a4-410">Для настройки маршрута добавьте после директивы `@page` шаблон маршрута, заключенный в двойные кавычки.</span><span class="sxs-lookup"><span data-stu-id="779a4-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="779a4-411">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="779a4-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="779a4-412">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="779a4-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="779a4-413">Символ `?` после `handler` означает, что параметр маршрута является необязательным.</span><span class="sxs-lookup"><span data-stu-id="779a4-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="779a4-414">Расширенная конфигурация и параметры</span><span class="sxs-lookup"><span data-stu-id="779a4-414">Advanced configuration and settings</span></span>

<span data-ttu-id="779a4-415">Конфигурация и параметры в следующих разделах не требуются большинству приложений.</span><span class="sxs-lookup"><span data-stu-id="779a4-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="779a4-416">Чтобы настроить дополнительные параметры, воспользуйтесь методом расширения <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span><span class="sxs-lookup"><span data-stu-id="779a4-416">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="779a4-417">Используйте <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>, чтобы задавать корневой каталог для страниц и добавлять для них соглашения моделей приложений.</span><span class="sxs-lookup"><span data-stu-id="779a4-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="779a4-418">Дополнительные сведения о соглашениях см. в разделе [Соглашения об авторизации Razor Pages](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="779a4-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="779a4-419">Сведения о предварительной компиляции представлений см. в [этой статье](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="779a4-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="779a4-420">Указание местонахождения Razor Pages в корне каталога</span><span class="sxs-lookup"><span data-stu-id="779a4-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="779a4-421">По умолчанию Razor Pages находится в корне каталога */Pages*.</span><span class="sxs-lookup"><span data-stu-id="779a4-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="779a4-422">Добавьте <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*>, чтобы указать, что Razor Pages находится в [корневой папке содержимого](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) приложения:</span><span class="sxs-lookup"><span data-stu-id="779a4-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="779a4-423">Указание местонахождения Razor Pages в пользовательском корневом каталоге</span><span class="sxs-lookup"><span data-stu-id="779a4-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="779a4-424">Добавьте <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*>, чтобы указать, что Razor Pages находится в пользовательском корневом каталоге в приложении (укажите относительный путь):</span><span class="sxs-lookup"><span data-stu-id="779a4-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="779a4-425">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="779a4-425">Additional resources</span></span>

* <span data-ttu-id="779a4-426">Дополнительные общие сведения см. в [этой статье](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="779a4-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* <span data-ttu-id="779a4-427">[Загрузить или просмотреть пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample).</span><span class="sxs-lookup"><span data-stu-id="779a4-427">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)</span></span>
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="779a4-428">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Райан Новак](https://github.com/rynowak) (Ryan Nowak)</span><span class="sxs-lookup"><span data-stu-id="779a4-428">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="779a4-429">Razor Pages — это новый аспект платформы MVC ASP.NET Core, который делает создание кодов сценариев для страниц проще и эффективнее.</span><span class="sxs-lookup"><span data-stu-id="779a4-429">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="779a4-430">Если вам нужно руководство, использующее подход "модель-представление-контроллер", см. статью [Начало работы с MVC в ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="779a4-430">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="779a4-431">Этот документ содержит вводные сведения о Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="779a4-431">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="779a4-432">Это не пошаговое руководство.</span><span class="sxs-lookup"><span data-stu-id="779a4-432">It's not a step by step tutorial.</span></span> <span data-ttu-id="779a4-433">Если некоторые разделы покажутся вам слишком сложными, см. [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="779a4-433">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="779a4-434">Общие сведения об ASP.NET Core см. в разделе [Введение в ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="779a4-434">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="779a4-435">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="779a4-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="779a4-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="779a4-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="779a4-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="779a4-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="779a4-438">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="779a4-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="779a4-439">Создание проекта Razor Pages</span><span class="sxs-lookup"><span data-stu-id="779a4-439">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="779a4-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="779a4-440">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="779a4-441">Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="779a4-441">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="779a4-442">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="779a4-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="779a4-443">Из командной строки выполните команду `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="779a4-443">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="779a4-444">Откройте созданный файл *.csproj* в Visual Studio для Mac.</span><span class="sxs-lookup"><span data-stu-id="779a4-444">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="779a4-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="779a4-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="779a4-446">Из командной строки выполните команду `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="779a4-446">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="779a4-447">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="779a4-447">Razor Pages</span></span>

<span data-ttu-id="779a4-448">Функция Razor Pages активируется в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="779a4-448">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="779a4-449">Рассмотрим простую страницу: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="779a4-449">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="779a4-450">Приведенный выше код выглядит как [файл представления Razor](xref:tutorials/first-mvc-app/adding-view), используемый в приложениях ASP.NET Core с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="779a4-450">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="779a4-451">и отличается от него только директивой `@page`.</span><span class="sxs-lookup"><span data-stu-id="779a4-451">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="779a4-452">Директива `@page` превращает файл в действие MVC, а значит обрабатывает запросы напрямую, минуя контроллер.</span><span class="sxs-lookup"><span data-stu-id="779a4-452">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="779a4-453">Директива `@page` должна быть первой директивой Razor на странице.</span><span class="sxs-lookup"><span data-stu-id="779a4-453">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="779a4-454">Директива `@page` влияет на поведение всех остальных конструкций Razor.</span><span class="sxs-lookup"><span data-stu-id="779a4-454">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="779a4-455">Похожая страница с использованием класса `PageModel` показана в следующих двух файлах.</span><span class="sxs-lookup"><span data-stu-id="779a4-455">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="779a4-456">Файл *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-456">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="779a4-457">Модель страницы *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="779a4-457">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="779a4-458">Как правило, файл класса `PageModel` называется так же, как файл Razor Pages, но с расширением *.cs*.</span><span class="sxs-lookup"><span data-stu-id="779a4-458">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="779a4-459">Например, представленная выше страница Razor Pages называется *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-459">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="779a4-460">Файл, содержащий класс `PageModel`, называется *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="779a4-460">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="779a4-461">Сопоставления URL-адресов со страницами определяются расположением конкретной страницы в файловой системе.</span><span class="sxs-lookup"><span data-stu-id="779a4-461">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="779a4-462">В приведенной ниже таблице показаны пути Razor Pages и соответствующие URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="779a4-462">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="779a4-463">Имя файла и путь</span><span class="sxs-lookup"><span data-stu-id="779a4-463">File name and path</span></span>               | <span data-ttu-id="779a4-464">Соответствующий URL</span><span class="sxs-lookup"><span data-stu-id="779a4-464">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="779a4-465">*/Pages/index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-465">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="779a4-466">`/` или `/Index`</span><span class="sxs-lookup"><span data-stu-id="779a4-466">`/` or `/Index`</span></span> |
| <span data-ttu-id="779a4-467">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-467">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="779a4-468">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-468">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="779a4-469">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-469">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="779a4-470">`/Store` или `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="779a4-470">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="779a4-471">Примечания.</span><span class="sxs-lookup"><span data-stu-id="779a4-471">Notes:</span></span>

* <span data-ttu-id="779a4-472">Среда выполнения по умолчанию ищет файлы Razor Pages в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="779a4-472">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="779a4-473">Если в URL-адресе не указана конкретная страница, по умолчанию открывается страница `Index`.</span><span class="sxs-lookup"><span data-stu-id="779a4-473">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="779a4-474">Создание простой формы</span><span class="sxs-lookup"><span data-stu-id="779a4-474">Write a basic form</span></span>

<span data-ttu-id="779a4-475">Функция Razor Pages предназначена для упрощения реализации типовых шаблонов, которые используются в браузерах, при создании приложения.</span><span class="sxs-lookup"><span data-stu-id="779a4-475">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="779a4-476">[Привязки модели](xref:mvc/models/model-binding), [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и вспомогательные методы HTML *отлично работают* со свойствами, определенными в классе Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="779a4-476">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="779a4-477">Рассмотрим страницу с простой формой связи для модели `Contact`.</span><span class="sxs-lookup"><span data-stu-id="779a4-477">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="779a4-478">В представленных в этой статье примерах `DbContext` инициализируется в файле [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="779a4-478">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="779a4-479">Модель данных:</span><span class="sxs-lookup"><span data-stu-id="779a4-479">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="779a4-480">Контекст базы данных:</span><span class="sxs-lookup"><span data-stu-id="779a4-480">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="779a4-481">Файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-481">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="779a4-482">Модель страницы *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="779a4-482">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="779a4-483">Как правило, класс `PageModel` называется `<PageName>Model` и находится в том же пространстве имен, что и страница.</span><span class="sxs-lookup"><span data-stu-id="779a4-483">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="779a4-484">Класс `PageModel` позволяет разделять логику страницы и ее представление.</span><span class="sxs-lookup"><span data-stu-id="779a4-484">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="779a4-485">Он определяет обработчики страницы для запросов, отправляемых на страницу, а также данные для ее визуализации.</span><span class="sxs-lookup"><span data-stu-id="779a4-485">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="779a4-486">Это разделение позволяет:</span><span class="sxs-lookup"><span data-stu-id="779a4-486">This separation allows:</span></span>

* <span data-ttu-id="779a4-487">управлять зависимостями страниц с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="779a4-487">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="779a4-488">[Модульное тестирование](xref:test/razor-pages-tests) страниц.</span><span class="sxs-lookup"><span data-stu-id="779a4-488">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="779a4-489">Страница содержит *метод обработчика* `OnPostAsync`, который выполняется по запросам `POST` (когда пользователь публикует форму).</span><span class="sxs-lookup"><span data-stu-id="779a4-489">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="779a4-490">Методы обработчика можно добавить для любой HTTP-команды.</span><span class="sxs-lookup"><span data-stu-id="779a4-490">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="779a4-491">Наиболее распространенные обработчики</span><span class="sxs-lookup"><span data-stu-id="779a4-491">The most common handlers are:</span></span>

* <span data-ttu-id="779a4-492">`OnGet` — инициализация необходимого для страницы состояния.</span><span class="sxs-lookup"><span data-stu-id="779a4-492">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="779a4-493">Пример обработчика [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="779a4-493">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="779a4-494">`OnPost` — обработка отправленных через форму данных.</span><span class="sxs-lookup"><span data-stu-id="779a4-494">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="779a4-495">Суффикс `Async` не является обязательным, но часто используется для асинхронных функций.</span><span class="sxs-lookup"><span data-stu-id="779a4-495">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="779a4-496">Этот код типичен для Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="779a4-496">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="779a4-497">Если вы знакомы с приложениями ASP.NET, использующими контроллеры и представления:</span><span class="sxs-lookup"><span data-stu-id="779a4-497">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="779a4-498">Код `OnPostAsync` в предыдущем примере похож на стандартный код контроллера.</span><span class="sxs-lookup"><span data-stu-id="779a4-498">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="779a4-499">Большинство примитивов MVC, включая [привязку модели](xref:mvc/models/model-binding), [проверку](xref:mvc/models/validation), [проверку](xref:mvc/models/validation) и результаты действий, являются общими.</span><span class="sxs-lookup"><span data-stu-id="779a4-499">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="779a4-500">Предыдущий метод `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="779a4-500">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="779a4-501">Простая схема `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="779a4-501">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="779a4-502">Проверка на наличие ошибок проверки.</span><span class="sxs-lookup"><span data-stu-id="779a4-502">Check for validation errors.</span></span>

* <span data-ttu-id="779a4-503">Если ошибок нет, сохранение данных и перенаправление.</span><span class="sxs-lookup"><span data-stu-id="779a4-503">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="779a4-504">Если есть ошибки, отображение страницы с сообщениями проверки.</span><span class="sxs-lookup"><span data-stu-id="779a4-504">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="779a4-505">Проверка на стороне клиента выполняется точно так же, как в традиционных приложениях MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="779a4-505">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="779a4-506">Во многих случаях ошибки проверки выявляются на клиентском компьютере и на сервер не отправляются.</span><span class="sxs-lookup"><span data-stu-id="779a4-506">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="779a4-507">После успешного ввода данных метод обработчика `OnPostAsync` вызывает метод обработчика `RedirectToPage`, чтобы получить экземпляр `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="779a4-507">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="779a4-508">`RedirectToPage` — это новый результат действия, аналогичный `RedirectToAction` или `RedirectToRoute`, но настроенный для страниц.</span><span class="sxs-lookup"><span data-stu-id="779a4-508">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="779a4-509">В приведенном выше примере он выполняет перенаправление на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="779a4-509">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="779a4-510">Более подробно `RedirectToPage` рассматривается в разделе [Создание URL для страниц](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="779a4-510">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="779a4-511">Если в отправленной форме имеются ошибки проверки (переданные на сервер), метод обработчика `OnPostAsync` вызывает метод обработчика `Page`.</span><span class="sxs-lookup"><span data-stu-id="779a4-511">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="779a4-512">`Page` возвращает экземпляр `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="779a4-512">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="779a4-513">Возвращение `Page` аналогично тому, как действия в контроллерах возвращают `View`.</span><span class="sxs-lookup"><span data-stu-id="779a4-513">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="779a4-514">`PageResult` — тип возвращаемого значения по умолчанию для метода обработчика.</span><span class="sxs-lookup"><span data-stu-id="779a4-514">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="779a4-515">Метод обработчика, вернувший `void`, визуализирует страницу.</span><span class="sxs-lookup"><span data-stu-id="779a4-515">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="779a4-516">Для указания согласия на привязку модели в свойстве `Customer` используется атрибут `[BindProperty]`.</span><span class="sxs-lookup"><span data-stu-id="779a4-516">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="779a4-517">По умолчанию функция Razor Pages привязывает свойства ко всем командам, кроме `GET`.</span><span class="sxs-lookup"><span data-stu-id="779a4-517">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="779a4-518">Привязка к свойствам позволяет сократить объем необходимого кода.</span><span class="sxs-lookup"><span data-stu-id="779a4-518">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="779a4-519">Привязка уменьшает код за счет того, что для визуализации полей формы (`<input asp-for="Customer.Name">`) и получения входных данных используется одно и то же свойство.</span><span class="sxs-lookup"><span data-stu-id="779a4-519">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="779a4-520">Домашняя страница (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="779a4-520">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="779a4-521">Связанный класс `PageModel` (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="779a4-521">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="779a4-522">Файл *Index.cshtml* содержит следующую разметку для создания ссылки на правку для каждого контактного лица.</span><span class="sxs-lookup"><span data-stu-id="779a4-522">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="779a4-523">[Вспомогательный тег привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` использовал атрибут `asp-route-{value}` для создания ссылки на страницу редактирования.</span><span class="sxs-lookup"><span data-stu-id="779a4-523">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="779a4-524">Эта ссылка содержит данные о маршруте с идентификатором контактного лица.</span><span class="sxs-lookup"><span data-stu-id="779a4-524">The link contains route data with the contact ID.</span></span> <span data-ttu-id="779a4-525">Например, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="779a4-525">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="779a4-526">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="779a4-526">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="779a4-527">Вспомогательные функции тегов включены с помощью `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="779a4-527">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="779a4-528">Файл *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-528">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="779a4-529">Первая строка содержит директиву `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="779a4-529">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="779a4-530">Ограничение маршрутизации `"{id:int}"` указывает, что страница должна принимать обращенные к ней запросы, которые содержат данные о маршруте `int`.</span><span class="sxs-lookup"><span data-stu-id="779a4-530">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="779a4-531">Если запрос к странице не содержит данные о маршруте, которые можно конвертировать в `int`, среда выполнения возвращает ошибку HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="779a4-531">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="779a4-532">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="779a4-532">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="779a4-533">Файл *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="779a4-533">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="779a4-534">Файл *Index.cshtml* также содержит разметку для создания кнопки удаления у каждого контакта клиента:</span><span class="sxs-lookup"><span data-stu-id="779a4-534">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="779a4-535">Во время обработки кнопки удаления в HTML ее `formaction` включает параметры для следующего:</span><span class="sxs-lookup"><span data-stu-id="779a4-535">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="779a4-536">Идентификатор контакта клиента, указанный атрибутом `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="779a4-536">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="779a4-537">Параметр `handler`, указанный атрибутом `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="779a4-537">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="779a4-538">Пример отображенной кнопки удаления с идентификатором контакта клиента `1`:</span><span class="sxs-lookup"><span data-stu-id="779a4-538">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="779a4-539">При выборе кнопки на сервер отправляется запрос формы `POST`.</span><span class="sxs-lookup"><span data-stu-id="779a4-539">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="779a4-540">По соглашению имя метода обработчика выбирается на основе значения параметра `handler` в соответствии со схемой `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="779a4-540">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="779a4-541">Так как `handler` — `delete` в этом примере, метод обработчика `OnPostDeleteAsync` используется для обработки запроса `POST`.</span><span class="sxs-lookup"><span data-stu-id="779a4-541">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="779a4-542">Если `asp-page-handler` имеет другое значение, например `remove`, выбирается метод обработчика с именем `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="779a4-542">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="779a4-543">В приведенном ниже коде показан обработчик `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="779a4-543">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="779a4-544">Метод `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="779a4-544">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="779a4-545">Принимает `id` из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="779a4-545">Accepts the `id` from the query string.</span></span> <span data-ttu-id="779a4-546">Если директива страницы *index.cshtml* содержит ограничение маршрутизации `"{id:int?}"`, то `id` будет получено из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="779a4-546">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="779a4-547">Данные маршрута для `id` указываются в универсальном коде ресурса (URI), например `https://localhost:5001/Customers/2`.</span><span class="sxs-lookup"><span data-stu-id="779a4-547">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="779a4-548">Отправляет в базу данных запрос контакта клиента с `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="779a4-548">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="779a4-549">Если контакт клиента найден, он удаляется из списка контактов.</span><span class="sxs-lookup"><span data-stu-id="779a4-549">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="779a4-550">База данных обновляется.</span><span class="sxs-lookup"><span data-stu-id="779a4-550">The database is updated.</span></span>
* <span data-ttu-id="779a4-551">Вызывает `RedirectToPage` для перенаправления на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="779a4-551">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="779a4-552">Маркировка свойств страницы как обязательных</span><span class="sxs-lookup"><span data-stu-id="779a4-552">Mark page properties as required</span></span>

<span data-ttu-id="779a4-553">Свойства класса `PageModel` можно отметить атрибутом [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute):</span><span class="sxs-lookup"><span data-stu-id="779a4-553">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="779a4-554">Дополнительные сведения см. в статье [Проверка модели](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="779a4-554">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="779a4-555">Обработка запросов HEAD с помощью вызова резервного обработчика OnGet</span><span class="sxs-lookup"><span data-stu-id="779a4-555">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="779a4-556">Запросы `HEAD` позволяют получать заголовки для определенного ресурса.</span><span class="sxs-lookup"><span data-stu-id="779a4-556">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="779a4-557">В отличие от запросов `GET` запросы `HEAD`не возвращают текст ответа.</span><span class="sxs-lookup"><span data-stu-id="779a4-557">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="779a4-558">Обработчик `OnHead` обычно создается и вызывается для выполнения запросов `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="779a4-558">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="779a4-559">Если в ASP.NET Core 2.1 или более поздней версии обработчик `OnHead` не определен, Razor Pages выполнит вызов резервного обработчика `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="779a4-559">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="779a4-560">Это поведение включается путем вызова [SetCompatibilityVersion](xref:mvc/compatibility-version) в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="779a4-560">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="779a4-561">Шаблоны по умолчанию создают вызов `SetCompatibilityVersion` в ASP.NET Core 2.1 и 2.2.</span><span class="sxs-lookup"><span data-stu-id="779a4-561">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="779a4-562">`SetCompatibilityVersion` задает для параметра `AllowMappingHeadRequestsToGetHandler` Razor Pages значение `true`.</span><span class="sxs-lookup"><span data-stu-id="779a4-562">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="779a4-563">Вместо включения всех возможных поведений с помощью метода `SetCompatibilityVersion` вы можете задать *конкретное* поведение.</span><span class="sxs-lookup"><span data-stu-id="779a4-563">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="779a4-564">Следующий код позволяет сопоставлять запросы `HEAD` с обработчиком `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="779a4-564">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="779a4-565">XSRF/CSRF и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="779a4-565">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="779a4-566">Вам не придется писать отдельный код для [проверки подлинности запросов](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="779a4-566">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="779a4-567">Razor Pages включает создание и проверку маркеров защиты от подделок по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="779a4-567">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="779a4-568">Использование макетов, частичных реплик, шаблонов и вспомогательных функций тегов с Razor Pages</span><span class="sxs-lookup"><span data-stu-id="779a4-568">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="779a4-569">Pages работает со всеми функциями подсистемы просмотра Razor.</span><span class="sxs-lookup"><span data-stu-id="779a4-569">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="779a4-570">Макеты, частичные реплики, шаблоны, вспомогательные функции тегов, а также файлы *_ViewStart.cshtml* и *_ViewImports.cshtml* работают точно так же, как и в стандартных представлениях Razor.</span><span class="sxs-lookup"><span data-stu-id="779a4-570">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="779a4-571">Давайте упростим нашу страницу с помощью некоторых из этих функций.</span><span class="sxs-lookup"><span data-stu-id="779a4-571">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="779a4-572">Добавим [макет страницы](xref:mvc/views/layout) в файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-572">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="779a4-573">Этот [макет](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="779a4-573">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="779a4-574">управляет макетом каждой страницы (кроме страниц с отказом от макета);</span><span class="sxs-lookup"><span data-stu-id="779a4-574">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="779a4-575">импортирует HTML-структуры, такие как JavaScript и таблицы стилей.</span><span class="sxs-lookup"><span data-stu-id="779a4-575">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="779a4-576">Дополнительные сведения см. в статье о [макете](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="779a4-576">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="779a4-577">Свойство [Layout](xref:mvc/views/layout#specifying-a-layout) определяется в файле *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-577">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="779a4-578">Макет хранится в папке *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="779a4-578">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="779a4-579">Pages ищет другие представления (макеты, шаблоны, частичные реплики) в иерархическом порядке, начиная с той папки, где находится текущая страница.</span><span class="sxs-lookup"><span data-stu-id="779a4-579">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="779a4-580">Макет в папке *Pages/Shared* можно использовать на любой странице Razor, которая находится в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="779a4-580">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="779a4-581">Файл макета следует поместить в папку *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="779a4-581">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="779a4-582">Корпорация Майкрософт рекомендует **не** размещать файл макета в папке *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="779a4-582">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="779a4-583">*Views/Shared* — это шаблон представлений MVC.</span><span class="sxs-lookup"><span data-stu-id="779a4-583">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="779a4-584">Razor Pages опирается на иерархию папок, а не на условные обозначения путей.</span><span class="sxs-lookup"><span data-stu-id="779a4-584">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="779a4-585">Поиск представлений в Razor Pages охватывает папку *Pages*.</span><span class="sxs-lookup"><span data-stu-id="779a4-585">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="779a4-586">Макеты, шаблоны и частичные реплики *работают* с контроллерами MVC и стандартными представлениями Razor.</span><span class="sxs-lookup"><span data-stu-id="779a4-586">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="779a4-587">Добавим файл *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-587">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="779a4-588">`@namespace` описывается далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="779a4-588">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="779a4-589">Директива `@addTagHelper` добавляет [встроенные вспомогательные теги](xref:mvc/views/tag-helpers/builtin-th/Index) на все страницы в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="779a4-589">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="779a4-590">Явное применение директивы `@namespace` на странице:</span><span class="sxs-lookup"><span data-stu-id="779a4-590">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="779a4-591">Данная директива задает пространство имен для страницы.</span><span class="sxs-lookup"><span data-stu-id="779a4-591">The directive sets the namespace for the page.</span></span> <span data-ttu-id="779a4-592">Включать пространство имен в директиву `@model` не требуется.</span><span class="sxs-lookup"><span data-stu-id="779a4-592">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="779a4-593">Если директива `@namespace` содержится в файле *_ViewImports.cshtml*, указанное пространство имен определяет префикс для созданного в Pages пространства имен, куда импортируется директива `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="779a4-593">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="779a4-594">Остальная часть созданного пространства имен (суффикс) представляет собой разделенный точками относительный путь между папкой с файлом *_ViewImports.cshtml* и папкой, содержащей страницу.</span><span class="sxs-lookup"><span data-stu-id="779a4-594">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="779a4-595">Например, класс `PageModel` в файле *Pages/Customers/Edit.cshtml.cs* задает пространство имен явно.</span><span class="sxs-lookup"><span data-stu-id="779a4-595">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="779a4-596">Файл *Pages/_ViewImports.cshtml* задает следующее пространство имен:</span><span class="sxs-lookup"><span data-stu-id="779a4-596">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="779a4-597">Сформированное пространство имен для файла *Pages/Customers/Edit.cshtml* Razor Pages совпадает с пространством имен класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="779a4-597">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="779a4-598">`@namespace` *также работает со стандартными представлениями Razor.*</span><span class="sxs-lookup"><span data-stu-id="779a4-598">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="779a4-599">Исходный файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-599">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="779a4-600">Обновленный файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="779a4-600">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="779a4-601">[Начальный проект Razor Pages](#rpvs17) содержит файл *Pages/_ValidationScriptsPartial.cshtml*, который подключает проверку на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="779a4-601">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="779a4-602">Дополнительные сведения о частичных представлениях см. в <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="779a4-602">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="779a4-603">Формирование URL-адресов для страниц</span><span class="sxs-lookup"><span data-stu-id="779a4-603">URL generation for Pages</span></span>

<span data-ttu-id="779a4-604">На представленной выше странице `Create` используется `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="779a4-604">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="779a4-605">Это приложение имеет следующую структуру файлов и папок:</span><span class="sxs-lookup"><span data-stu-id="779a4-605">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="779a4-606">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="779a4-606">*/Pages*</span></span>

  * <span data-ttu-id="779a4-607">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-607">*Index.cshtml*</span></span>
  * <span data-ttu-id="779a4-608">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="779a4-608">*/Customers*</span></span>

    * <span data-ttu-id="779a4-609">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-609">*Create.cshtml*</span></span>
    * <span data-ttu-id="779a4-610">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-610">*Edit.cshtml*</span></span>
    * <span data-ttu-id="779a4-611">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="779a4-611">*Index.cshtml*</span></span>

<span data-ttu-id="779a4-612">После успешного выполнения страницы *Pages/Customers/Create.cshtml* и *Pages/Customers/Edit.cshtml* перенаправляются на страницу *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-612">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="779a4-613">Строка `/Index` составляет часть URL-адреса для доступа к указанной выше странице.</span><span class="sxs-lookup"><span data-stu-id="779a4-613">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="779a4-614">Строка `/Index` позволяет создавать URL-адреса для страницы *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-614">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="779a4-615">Пример:</span><span class="sxs-lookup"><span data-stu-id="779a4-615">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="779a4-616">Имя страницы — это путь к странице из корневой папки */Pages*, включая начальный символ `/` (например, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="779a4-616">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="779a4-617">Предыдущие образцы создания URL-адреса обеспечивают расширенные параметры и функциональные возможности по сравнению с жестким заданием URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="779a4-617">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="779a4-618">Формирование URL-адресов включает [маршрутизацию](xref:mvc/controllers/routing) и позволяет генерировать и включать в код параметры в зависимости от того, как определяется маршрут в пути назначения.</span><span class="sxs-lookup"><span data-stu-id="779a4-618">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="779a4-619">Формирование URL-адресов для страниц поддерживает относительные имена.</span><span class="sxs-lookup"><span data-stu-id="779a4-619">URL generation for pages supports relative names.</span></span> <span data-ttu-id="779a4-620">В приведенной ниже таблице показано, какая страница индекса выбирается с каждым параметром `RedirectToPage` в *Pages/Customers/Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="779a4-620">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="779a4-621">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="779a4-621">RedirectToPage(x)</span></span>| <span data-ttu-id="779a4-622">Страница</span><span class="sxs-lookup"><span data-stu-id="779a4-622">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="779a4-623">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="779a4-623">RedirectToPage("/Index")</span></span> | <span data-ttu-id="779a4-624">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="779a4-624">*Pages/Index*</span></span> |
| <span data-ttu-id="779a4-625">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="779a4-625">RedirectToPage("./Index");</span></span> | <span data-ttu-id="779a4-626">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="779a4-626">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="779a4-627">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="779a4-627">RedirectToPage("../Index")</span></span> | <span data-ttu-id="779a4-628">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="779a4-628">*Pages/Index*</span></span> |
| <span data-ttu-id="779a4-629">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="779a4-629">RedirectToPage("Index")</span></span>  | <span data-ttu-id="779a4-630">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="779a4-630">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="779a4-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")` и `RedirectToPage("../Index")` — это *относительные имена*.</span><span class="sxs-lookup"><span data-stu-id="779a4-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="779a4-632">Для получения имени целевой страницы параметр `RedirectToPage`*комбинируется* с путем текущей страницы.</span><span class="sxs-lookup"><span data-stu-id="779a4-632">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="779a4-633">Привязка относительных имен полезна при создании сайтов со сложной структурой.</span><span class="sxs-lookup"><span data-stu-id="779a4-633">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="779a4-634">Если для связи между страницами в определенной папке используются относительные имена, эту папку можно переименовать.</span><span class="sxs-lookup"><span data-stu-id="779a4-634">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="779a4-635">При этом все ссылки останутся рабочими (так как не включают имя папки).</span><span class="sxs-lookup"><span data-stu-id="779a4-635">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="779a4-636">Чтобы выполнить перенаправление на страницу в другой [области](xref:mvc/controllers/areas), укажите эту область:</span><span class="sxs-lookup"><span data-stu-id="779a4-636">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="779a4-637">Для получения дополнительной информации см. <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="779a4-637">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="779a4-638">Атрибут ViewData</span><span class="sxs-lookup"><span data-stu-id="779a4-638">ViewData attribute</span></span>

<span data-ttu-id="779a4-639">Данные могут передаваться на страницу с помощью атрибута [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="779a4-639">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="779a4-640">Свойства в контроллерах или моделях Razor Page, отмеченные атрибутом `[ViewData]`, обладают собственными значениями, загружаемыми из [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="779a4-640">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="779a4-641">В следующем примере класс `AboutModel` содержит свойство `Title`, отмеченное атрибутом `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="779a4-641">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="779a4-642">Свойство `Title` задает заголовок страницы About.</span><span class="sxs-lookup"><span data-stu-id="779a4-642">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="779a4-643">На странице About доступ к свойству `Title` осуществляется как доступ к свойству модели.</span><span class="sxs-lookup"><span data-stu-id="779a4-643">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="779a4-644">В макете заголовок считывается из словаря ViewData.</span><span class="sxs-lookup"><span data-stu-id="779a4-644">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="779a4-645">TempData</span><span class="sxs-lookup"><span data-stu-id="779a4-645">TempData</span></span>

<span data-ttu-id="779a4-646">ASP.NET Core позволяет использовать свойство [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) в [контроллере](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="779a4-646">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="779a4-647">Это свойство хранит данные до тех пор, пока они не будут прочитаны.</span><span class="sxs-lookup"><span data-stu-id="779a4-647">This property stores data until it's read.</span></span> <span data-ttu-id="779a4-648">Для проверки данных без удаления можно использовать методы `Keep` и `Peek`.</span><span class="sxs-lookup"><span data-stu-id="779a4-648">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="779a4-649">`TempData` удобно использовать для перенаправления, когда данные требуются больше чем для одного запроса.</span><span class="sxs-lookup"><span data-stu-id="779a4-649">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="779a4-650">В следующем коде значение `Message` задается с помощью `TempData`:</span><span class="sxs-lookup"><span data-stu-id="779a4-650">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="779a4-651">Представленная ниже разметка в файле *Pages/Customers/Index.cshtml* отображает значение `Message` с помощью `TempData`.</span><span class="sxs-lookup"><span data-stu-id="779a4-651">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="779a4-652">Модель страницы *Pages/Customers/Index.cshtml.cs* применяет атрибут `[TempData]` к свойству `Message`.</span><span class="sxs-lookup"><span data-stu-id="779a4-652">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="779a4-653">Дополнительные сведения см. в разделе [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="779a4-653">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="779a4-654">Несколько обработчиков на страницу</span><span class="sxs-lookup"><span data-stu-id="779a4-654">Multiple handlers per page</span></span>

<span data-ttu-id="779a4-655">Следующая страница формирует разметку для двух обработчиков с помощью вспомогательной функции тегов `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="779a4-655">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="779a4-656">Форма в предыдущем примере включает две кнопки отправки, каждая из которых отправляет данные на отдельный URL-адрес с помощью `FormActionTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="779a4-656">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="779a4-657">Атрибут `asp-page-handler` является дополнением к `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="779a4-657">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="779a4-658">Атрибут `asp-page-handler` формирует URL-адреса, ,которые используются для отправки данных в каждый из методов обработчиков, определенных страницей.</span><span class="sxs-lookup"><span data-stu-id="779a4-658">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="779a4-659">`asp-page` не задается, так как пример сопоставлен с текущей страницей.</span><span class="sxs-lookup"><span data-stu-id="779a4-659">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="779a4-660">Модель страницы</span><span class="sxs-lookup"><span data-stu-id="779a4-660">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="779a4-661">В представленном выше коде используются *именованные методы обработчика*.</span><span class="sxs-lookup"><span data-stu-id="779a4-661">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="779a4-662">Именованные методы обработчика создаются путем размещения определенного текста в имени после `On<HTTP Verb>` и перед `Async` (если есть).</span><span class="sxs-lookup"><span data-stu-id="779a4-662">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="779a4-663">В приведенном выше примере использовались методы страницы OnPost**JoinList**Async и OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="779a4-663">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="779a4-664">Если убрать *OnPost* и *Async*, имена обработчиков будут выглядеть как `JoinList` и `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="779a4-664">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="779a4-665">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="779a4-665">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="779a4-666">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="779a4-666">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="779a4-667">Пользовательские маршруты</span><span class="sxs-lookup"><span data-stu-id="779a4-667">Custom routes</span></span>

<span data-ttu-id="779a4-668">С помощью директивы `@page` можно сделать следующее.</span><span class="sxs-lookup"><span data-stu-id="779a4-668">Use the `@page` directive to:</span></span>

* <span data-ttu-id="779a4-669">Указать пользовательский маршрут к странице.</span><span class="sxs-lookup"><span data-stu-id="779a4-669">Specify a custom route to a page.</span></span> <span data-ttu-id="779a4-670">Например, можно задать маршрут к странице "Сведения" `/Some/Other/Path`: `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="779a4-670">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="779a4-671">Добавить сегменты к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="779a4-671">Append segments to a page's default route.</span></span> <span data-ttu-id="779a4-672">Например, к такому маршруту можно добавить сегмент item: `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="779a4-672">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="779a4-673">Добавить параметры к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="779a4-673">Append parameters to a page's default route.</span></span> <span data-ttu-id="779a4-674">Например, для страницы с `@page "{id}"` может потребоваться параметр идентификатора `id`.</span><span class="sxs-lookup"><span data-stu-id="779a4-674">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="779a4-675">Поддерживается путь относительно корня, заданный знаком тильды (`~`) в начале пути.</span><span class="sxs-lookup"><span data-stu-id="779a4-675">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="779a4-676">Например, `@page "~/Some/Other/Path"` равносильно `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="779a4-676">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="779a4-677">Если вы не хотите, чтобы в URL-адресе отображалась строка запроса `?handler=JoinList`, измените маршрут так, чтобы в качестве пути в URL-адресе указывалось имя обработчика.</span><span class="sxs-lookup"><span data-stu-id="779a4-677">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="779a4-678">Для настройки маршрута добавьте после директивы `@page` шаблон маршрута, заключенный в двойные кавычки.</span><span class="sxs-lookup"><span data-stu-id="779a4-678">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="779a4-679">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="779a4-679">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="779a4-680">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="779a4-680">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="779a4-681">Символ `?` после `handler` означает, что параметр маршрута является необязательным.</span><span class="sxs-lookup"><span data-stu-id="779a4-681">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="779a4-682">Конфигурация и параметры</span><span class="sxs-lookup"><span data-stu-id="779a4-682">Configuration and settings</span></span>

<span data-ttu-id="779a4-683">Чтобы настроить дополнительные параметры, воспользуйтесь методом расширения `AddRazorPagesOptions` в построителе MVC:</span><span class="sxs-lookup"><span data-stu-id="779a4-683">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="779a4-684">В настоящее время `RazorPagesOptions` позволяет задавать корневую папку для страниц и добавлять для них обозначения моделей приложений.</span><span class="sxs-lookup"><span data-stu-id="779a4-684">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="779a4-685">В дальнейшем мы расширим спектр его возможностей.</span><span class="sxs-lookup"><span data-stu-id="779a4-685">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="779a4-686">Сведения о предварительной компиляции представлений см. на странице [Компиляция представлений Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="779a4-686">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="779a4-687">[Загрузить или просмотреть пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="779a4-687">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="779a4-688">Дополнительные общие сведения см. на странице [Учебник. Начало работы с Razor Pages в ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="779a4-688">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="779a4-689">Указание местонахождения Razor Pages в корне каталога</span><span class="sxs-lookup"><span data-stu-id="779a4-689">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="779a4-690">По умолчанию Razor Pages находится в корне каталога */Pages*.</span><span class="sxs-lookup"><span data-stu-id="779a4-690">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="779a4-691">Добавьте [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) в [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_), чтобы указать, что Razor Pages находится в [корневой папке содержимого](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) приложения:</span><span class="sxs-lookup"><span data-stu-id="779a4-691">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="779a4-692">Указание местонахождения Razor Pages в пользовательском корневом каталоге</span><span class="sxs-lookup"><span data-stu-id="779a4-692">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="779a4-693">Добавьте [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) в [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_), чтобы указать, что Razor Pages находится в пользовательском корневом каталоге в приложении (укажите относительный путь):</span><span class="sxs-lookup"><span data-stu-id="779a4-693">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="779a4-694">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="779a4-694">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
