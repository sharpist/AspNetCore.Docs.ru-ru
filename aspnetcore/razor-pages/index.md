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
ms.openlocfilehash: 52c3dc82e51cb4375954a603a1bfde60fd667b56
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103047"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="113c6-103">Введение в Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="113c6-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="113c6-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Райан Новак](https://github.com/rynowak) (Ryan Nowak)</span><span class="sxs-lookup"><span data-stu-id="113c6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

Razor<span data-ttu-id="113c6-105"> Pages делает создание кодов сценариев для страниц проще и эффективнее по сравнению с использованием контроллеров и представлений.</span><span class="sxs-lookup"><span data-stu-id="113c6-105"> Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="113c6-106">Если вам нужно руководство, использующее подход "модель-представление-контроллер", см. статью [Начало работы с MVC в ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="113c6-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="113c6-107">Этот документ содержит вводные сведения о Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="113c6-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="113c6-108">Это не пошаговое руководство.</span><span class="sxs-lookup"><span data-stu-id="113c6-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="113c6-109">Если некоторые разделы покажутся вам слишком сложными, см. [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="113c6-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="113c6-110">Общие сведения об ASP.NET Core см. в разделе [Введение в ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="113c6-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="113c6-111">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="113c6-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="113c6-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="113c6-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="113c6-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="113c6-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="113c6-114">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="113c6-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="113c6-115">Создание проекта Razor Pages</span><span class="sxs-lookup"><span data-stu-id="113c6-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="113c6-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="113c6-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="113c6-117">Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="113c6-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="113c6-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="113c6-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="113c6-119">Из командной строки выполните команду `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="113c6-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="113c6-120">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="113c6-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="113c6-121">Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="113c6-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a>Razor<span data-ttu-id="113c6-122"> Pages</span><span class="sxs-lookup"><span data-stu-id="113c6-122"> Pages</span></span>

Razor<span data-ttu-id="113c6-123"> Pages активируется в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="113c6-123"> Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="113c6-124">Рассмотрим простую страницу: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="113c6-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="113c6-125">Приведенный выше код выглядит как [Razor файл представления](xref:tutorials/first-mvc-app/adding-view), используемый в приложениях ASP.NET Core с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="113c6-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="113c6-126">Он отличается от него только директивой [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="113c6-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="113c6-127">Директива `@page` превращает файл в действие MVC, а значит обрабатывает запросы напрямую, минуя контроллер.</span><span class="sxs-lookup"><span data-stu-id="113c6-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="113c6-128">`@page` должна быть первой директивой Razor на странице.</span><span class="sxs-lookup"><span data-stu-id="113c6-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="113c6-129">`@page` влияет на поведение всех остальных конструкций [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="113c6-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="113c6-130">Имена файлов Razor Pages имеют суффикс *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="113c6-131">Похожая страница с использованием класса `PageModel` показана в следующих двух файлах.</span><span class="sxs-lookup"><span data-stu-id="113c6-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="113c6-132">Файл *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="113c6-133">Модель страницы *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="113c6-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="113c6-134">Как правило, файл класса `PageModel` называется так же, как файл Razor Pages, но с расширением *CS*.</span><span class="sxs-lookup"><span data-stu-id="113c6-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="113c6-135">Например, представленная выше страница Razor Pages называется *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="113c6-136">Файл, содержащий класс `PageModel`, называется *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="113c6-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="113c6-137">Сопоставления URL-адресов со страницами определяются расположением конкретной страницы в файловой системе.</span><span class="sxs-lookup"><span data-stu-id="113c6-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="113c6-138">В приведенной ниже таблице показаны пути Razor Pages и соответствующие URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="113c6-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="113c6-139">Имя файла и путь</span><span class="sxs-lookup"><span data-stu-id="113c6-139">File name and path</span></span>               | <span data-ttu-id="113c6-140">Соответствующий URL</span><span class="sxs-lookup"><span data-stu-id="113c6-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="113c6-141">*/Pages/index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="113c6-142">`/` или `/Index`</span><span class="sxs-lookup"><span data-stu-id="113c6-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="113c6-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="113c6-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="113c6-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="113c6-146">`/Store` или `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="113c6-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="113c6-147">Примечания.</span><span class="sxs-lookup"><span data-stu-id="113c6-147">Notes:</span></span>

* <span data-ttu-id="113c6-148">Среда выполнения по умолчанию ищет файлы Razor Pages в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="113c6-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="113c6-149">Если в URL-адресе не указана конкретная страница, по умолчанию открывается страница `Index`.</span><span class="sxs-lookup"><span data-stu-id="113c6-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="113c6-150">Создание простой формы</span><span class="sxs-lookup"><span data-stu-id="113c6-150">Write a basic form</span></span>

Razor<span data-ttu-id="113c6-151"> Pages предназначена для упрощения реализации типовых шаблонов, которые используются в браузерах, при создании приложения.</span><span class="sxs-lookup"><span data-stu-id="113c6-151"> Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="113c6-152">[Привязки модели](xref:mvc/models/model-binding), [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и вспомогательные методы HTML *отлично работают* со свойствами, определенными в классе Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="113c6-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="113c6-153">Рассмотрим страницу с простой формой связи для модели `Contact`.</span><span class="sxs-lookup"><span data-stu-id="113c6-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="113c6-154">В представленных в этой статье примерах `DbContext` инициализируется в файле [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="113c6-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="113c6-155">Модель данных:</span><span class="sxs-lookup"><span data-stu-id="113c6-155">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="113c6-156">Контекст базы данных:</span><span class="sxs-lookup"><span data-stu-id="113c6-156">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="113c6-157">Файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="113c6-158">Модель страницы *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="113c6-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="113c6-159">Как правило, класс `PageModel` называется `<PageName>Model` и находится в том же пространстве имен, что и страница.</span><span class="sxs-lookup"><span data-stu-id="113c6-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="113c6-160">Класс `PageModel` позволяет разделять логику страницы и ее представление.</span><span class="sxs-lookup"><span data-stu-id="113c6-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="113c6-161">Он определяет обработчики страницы для запросов, отправляемых на страницу, а также данные для ее визуализации.</span><span class="sxs-lookup"><span data-stu-id="113c6-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="113c6-162">Это разделение позволяет:</span><span class="sxs-lookup"><span data-stu-id="113c6-162">This separation allows:</span></span>

* <span data-ttu-id="113c6-163">управлять зависимостями страниц с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="113c6-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="113c6-164">Модульное тестирование</span><span class="sxs-lookup"><span data-stu-id="113c6-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="113c6-165">Страница содержит *метод обработчика* `OnPostAsync`, который выполняется по запросам `POST` (когда пользователь публикует форму).</span><span class="sxs-lookup"><span data-stu-id="113c6-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="113c6-166">Можно добавить методы обработчика для любой HTTP-команды.</span><span class="sxs-lookup"><span data-stu-id="113c6-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="113c6-167">Наиболее распространенные обработчики</span><span class="sxs-lookup"><span data-stu-id="113c6-167">The most common handlers are:</span></span>

* <span data-ttu-id="113c6-168">`OnGet` — инициализация необходимого для страницы состояния.</span><span class="sxs-lookup"><span data-stu-id="113c6-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="113c6-169">В приведенном выше коде метод `OnGet` отображает страницу Razor *CreateModel.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="113c6-170">`OnPost` — обработка отправленных через форму данных.</span><span class="sxs-lookup"><span data-stu-id="113c6-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="113c6-171">Суффикс `Async` не является обязательным, но часто используется для асинхронных функций.</span><span class="sxs-lookup"><span data-stu-id="113c6-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="113c6-172">Этот код типичен для Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="113c6-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="113c6-173">Если вы знакомы с приложениями ASP.NET, использующими контроллеры и представления:</span><span class="sxs-lookup"><span data-stu-id="113c6-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="113c6-174">Код `OnPostAsync` в предыдущем примере похож на стандартный код контроллера.</span><span class="sxs-lookup"><span data-stu-id="113c6-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="113c6-175">Большинство примитивов MVC, включая [привязку модели](xref:mvc/models/model-binding), [проверку](xref:mvc/models/validation) и результаты действий, одинаково работают с контроллерами и Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="113c6-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="113c6-176">Предыдущий метод `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="113c6-176">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="113c6-177">Простая схема `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="113c6-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="113c6-178">Проверка на наличие ошибок проверки.</span><span class="sxs-lookup"><span data-stu-id="113c6-178">Check for validation errors.</span></span>

* <span data-ttu-id="113c6-179">Если ошибок нет, сохранение данных и перенаправление.</span><span class="sxs-lookup"><span data-stu-id="113c6-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="113c6-180">Если есть ошибки, отображение страницы с сообщениями проверки.</span><span class="sxs-lookup"><span data-stu-id="113c6-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="113c6-181">Во многих случаях ошибки проверки выявляются на клиентском компьютере и на сервер не отправляются.</span><span class="sxs-lookup"><span data-stu-id="113c6-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="113c6-182">Файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="113c6-183">Преобразованный HTML из *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="113c6-184">В приведенном выше коде, разместив форму:</span><span class="sxs-lookup"><span data-stu-id="113c6-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="113c6-185">С допустимыми данными:</span><span class="sxs-lookup"><span data-stu-id="113c6-185">With valid data:</span></span>

  * <span data-ttu-id="113c6-186">Метод обработчика `OnPostAsync` вызывает вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>.</span><span class="sxs-lookup"><span data-stu-id="113c6-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="113c6-187">`RedirectToPage` возвращает экземпляр <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="113c6-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="113c6-188">`RedirectToPage`.</span><span class="sxs-lookup"><span data-stu-id="113c6-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="113c6-189">Является результатом действия.</span><span class="sxs-lookup"><span data-stu-id="113c6-189">Is an action result.</span></span>
    * <span data-ttu-id="113c6-190">Аналогичен `RedirectToAction` или `RedirectToRoute` (используется в контроллерах и представлениях).</span><span class="sxs-lookup"><span data-stu-id="113c6-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="113c6-191">Настраивается для страниц.</span><span class="sxs-lookup"><span data-stu-id="113c6-191">Is customized for pages.</span></span> <span data-ttu-id="113c6-192">В приведенном выше примере он выполняет перенаправление на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="113c6-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="113c6-193">Более подробно `RedirectToPage` рассматривается в разделе [Создание URL для страниц](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="113c6-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="113c6-194">С ошибками проверки, передаваемыми на сервер:</span><span class="sxs-lookup"><span data-stu-id="113c6-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="113c6-195">Метод обработчика `OnPostAsync` вызывает вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>.</span><span class="sxs-lookup"><span data-stu-id="113c6-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="113c6-196">`Page` возвращает экземпляр <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="113c6-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="113c6-197">Возвращение `Page` аналогично тому, как действия в контроллерах возвращают `View`.</span><span class="sxs-lookup"><span data-stu-id="113c6-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="113c6-198">`PageResult` — тип возвращаемого значения по умолчанию для метода обработчика.</span><span class="sxs-lookup"><span data-stu-id="113c6-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="113c6-199">Метод обработчика, вернувший `void`, визуализирует страницу.</span><span class="sxs-lookup"><span data-stu-id="113c6-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="113c6-200">В предыдущем примере публикация формы без значения приводит к тому, что [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) возвращает значение false.</span><span class="sxs-lookup"><span data-stu-id="113c6-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="113c6-201">В этом примере на клиенте не отображаются ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="113c6-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="113c6-202">Обработка ошибок проверки рассматривается далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="113c6-202">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="113c6-203">С ошибками проверки, обнаруженными при проверке на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="113c6-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="113c6-204">Данные **не** отправляются на сервер.</span><span class="sxs-lookup"><span data-stu-id="113c6-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="113c6-205">Проверка на стороне клиента описана далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="113c6-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="113c6-206">Для указания согласия на привязку модели в свойстве `Customer` используется атрибут [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute):</span><span class="sxs-lookup"><span data-stu-id="113c6-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="113c6-207">`[BindProperty]`**не** следует использовать в моделях, содержащих свойства, которые не должны изменяться клиентом.</span><span class="sxs-lookup"><span data-stu-id="113c6-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="113c6-208">Дополнительные сведения см. в [этом разделе](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="113c6-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="113c6-209">По умолчанию Razor привязывает свойства ко всем командам, кроме `GET`.</span><span class="sxs-lookup"><span data-stu-id="113c6-209">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="113c6-210">Привязка к свойствам устраняет необходимость в написании кода для преобразования данных HTTP в тип модели.</span><span class="sxs-lookup"><span data-stu-id="113c6-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="113c6-211">Привязка уменьшает код за счет того, что для визуализации полей формы (`<input asp-for="Customer.Name">`) и получения входных данных используется одно и то же свойство.</span><span class="sxs-lookup"><span data-stu-id="113c6-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="113c6-212">Просмотр файла представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="113c6-213">В приведенном выше коде [вспомогательная функция тега ввода](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` привязывает элемент `<input>` HTML к выражению модели `Customer.Name`.</span><span class="sxs-lookup"><span data-stu-id="113c6-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="113c6-214">Директива [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) обеспечивает доступность вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="113c6-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="113c6-215">Домашняя страница</span><span class="sxs-lookup"><span data-stu-id="113c6-215">The home page</span></span>

<span data-ttu-id="113c6-216">*Index.cshtml* — это домашняя страница:</span><span class="sxs-lookup"><span data-stu-id="113c6-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="113c6-217">Связанный класс `PageModel` (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="113c6-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="113c6-218">Файл *index.cshtml* содержит следующую разметку:</span><span class="sxs-lookup"><span data-stu-id="113c6-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="113c6-219">[Вспомогательный тег привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a /a>` использовал атрибут `asp-route-{value}` для создания ссылки на страницу редактирования.</span><span class="sxs-lookup"><span data-stu-id="113c6-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="113c6-220">Эта ссылка содержит данные о маршруте с идентификатором контактного лица.</span><span class="sxs-lookup"><span data-stu-id="113c6-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="113c6-221">Например, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="113c6-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="113c6-222">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="113c6-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="113c6-223">Файл *Index.cshtml* содержит разметку для создания кнопки удаления у каждого контакта клиента:</span><span class="sxs-lookup"><span data-stu-id="113c6-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="113c6-224">Отображаемый HTML:</span><span class="sxs-lookup"><span data-stu-id="113c6-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="113c6-225">Во время обработки кнопки удаления в HTML ее [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) включает параметры для следующего:</span><span class="sxs-lookup"><span data-stu-id="113c6-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="113c6-226">Идентификатор контакта клиента, указанный атрибутом `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="113c6-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="113c6-227">Параметр `handler`, указанный атрибутом `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="113c6-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="113c6-228">При выборе кнопки на сервер отправляется запрос формы `POST`.</span><span class="sxs-lookup"><span data-stu-id="113c6-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="113c6-229">По соглашению имя метода обработчика выбирается на основе значения параметра `handler` в соответствии со схемой `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="113c6-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="113c6-230">Так как `handler` — `delete` в этом примере, метод обработчика `OnPostDeleteAsync` используется для обработки запроса `POST`.</span><span class="sxs-lookup"><span data-stu-id="113c6-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="113c6-231">Если `asp-page-handler` имеет другое значение, например `remove`, выбирается метод обработчика с именем `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="113c6-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="113c6-232">Метод `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="113c6-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="113c6-233">Получает `id` из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="113c6-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="113c6-234">Отправляет в базу данных запрос контакта клиента с `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="113c6-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="113c6-235">Если контакт клиента найден, он удаляется, и база данных обновляется.</span><span class="sxs-lookup"><span data-stu-id="113c6-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="113c6-236">Вызывает <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> для перенаправления на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="113c6-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="113c6-237">Файл Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="113c6-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="113c6-238">Первая строка содержит директиву `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="113c6-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="113c6-239">Ограничение маршрутизации `"{id:int}"` указывает, что страница должна принимать обращенные к ней запросы, которые содержат данные о маршруте `int`.</span><span class="sxs-lookup"><span data-stu-id="113c6-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="113c6-240">Если запрос к странице не содержит данные о маршруте, которые можно конвертировать в `int`, среда выполнения возвращает ошибку HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="113c6-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="113c6-241">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="113c6-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="113c6-242">Файл *Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="113c6-242">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="113c6-243">Проверка</span><span class="sxs-lookup"><span data-stu-id="113c6-243">Validation</span></span>

<span data-ttu-id="113c6-244">Правила проверки:</span><span class="sxs-lookup"><span data-stu-id="113c6-244">Validation rules:</span></span>

* <span data-ttu-id="113c6-245">Декларативно задаются в классе Model.</span><span class="sxs-lookup"><span data-stu-id="113c6-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="113c6-246">Применяются везде в приложении.</span><span class="sxs-lookup"><span data-stu-id="113c6-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="113c6-247">Пространство имен <xref:System.ComponentModel.DataAnnotations> предоставляет набор встроенных атрибутов проверки, которые декларативно применяются к классу или свойству.</span><span class="sxs-lookup"><span data-stu-id="113c6-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="113c6-248">Кроме того, DataAnnotations содержит атрибуты форматирования (такие как [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)), которые обеспечивают форматирование и не предназначены для проверки.</span><span class="sxs-lookup"><span data-stu-id="113c6-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="113c6-249">Рассмотрим модель `Customer`:</span><span class="sxs-lookup"><span data-stu-id="113c6-249">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="113c6-250">Используя следующий файл представления *Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="113c6-251">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="113c6-251">The preceding code:</span></span>

* <span data-ttu-id="113c6-252">Включает jQuery и скрипты проверки jQuery.</span><span class="sxs-lookup"><span data-stu-id="113c6-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="113c6-253">Использует [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) `<div />` и `<span />` для следующих задач:</span><span class="sxs-lookup"><span data-stu-id="113c6-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="113c6-254">Проверка на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="113c6-254">Client-side validation.</span></span>
  * <span data-ttu-id="113c6-255">Отображение ошибок при проверке.</span><span class="sxs-lookup"><span data-stu-id="113c6-255">Validation error rendering.</span></span>

* <span data-ttu-id="113c6-256">Генерирует следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="113c6-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="113c6-257">При публикации формы создания без значения имени в форме отображается сообщение об ошибке: "Поле имени является обязательным".</span><span class="sxs-lookup"><span data-stu-id="113c6-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="113c6-258">в форме.</span><span class="sxs-lookup"><span data-stu-id="113c6-258">on the form.</span></span> <span data-ttu-id="113c6-259">Если на клиенте включен JavaScript, браузер отображает ошибку без отправки на сервер.</span><span class="sxs-lookup"><span data-stu-id="113c6-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="113c6-260">Атрибут `[StringLength(10)]` создает `data-val-length-max="10"` в отображаемом HTML-коде.</span><span class="sxs-lookup"><span data-stu-id="113c6-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="113c6-261">`data-val-length-max` не дает браузерам ввести больше заданной максимальной длины.</span><span class="sxs-lookup"><span data-stu-id="113c6-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="113c6-262">Если для изменения и воспроизведения записи используется средство, например [Fiddler](https://www.telerik.com/fiddler), выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="113c6-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="113c6-263">С именем, превышающим 10.</span><span class="sxs-lookup"><span data-stu-id="113c6-263">With the name longer than 10.</span></span>
* <span data-ttu-id="113c6-264">Возвращается сообщение об ошибке: "Имя поля должно быть строкой с максимальной длиной 10".</span><span class="sxs-lookup"><span data-stu-id="113c6-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="113c6-265">.</span><span class="sxs-lookup"><span data-stu-id="113c6-265">is returned.</span></span>

<span data-ttu-id="113c6-266">Рассмотрим следующую модель `Movie`:</span><span class="sxs-lookup"><span data-stu-id="113c6-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="113c6-267">Атрибуты проверки определяют поведение для свойств модели, к которым они применяются:</span><span class="sxs-lookup"><span data-stu-id="113c6-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="113c6-268">Атрибуты `Required` и `MinimumLength` указывают, что свойство должно иметь значение. Тем не менее, чтобы удовлетворить требованиям проверки, пользователю достаточно ввести пробел.</span><span class="sxs-lookup"><span data-stu-id="113c6-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="113c6-269">Атрибут `RegularExpression` ограничивает набор допустимых для ввода символов.</span><span class="sxs-lookup"><span data-stu-id="113c6-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="113c6-270">В приведенном выше коде в Genre:</span><span class="sxs-lookup"><span data-stu-id="113c6-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="113c6-271">должны использоваться только буквы;</span><span class="sxs-lookup"><span data-stu-id="113c6-271">Must only use letters.</span></span>
  * <span data-ttu-id="113c6-272">первая буква должна быть прописной;</span><span class="sxs-lookup"><span data-stu-id="113c6-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="113c6-273">пробелы, цифры и специальные символы не допускаются.</span><span class="sxs-lookup"><span data-stu-id="113c6-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="113c6-274">В `RegularExpression` Rating:</span><span class="sxs-lookup"><span data-stu-id="113c6-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="113c6-275">первый символ должен быть прописной буквой;</span><span class="sxs-lookup"><span data-stu-id="113c6-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="113c6-276">допускаются специальные символы и цифры, а также последующие пробелы.</span><span class="sxs-lookup"><span data-stu-id="113c6-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="113c6-277">Значение "PG-13" допустимо для рейтинга, но недопустимо для жанра.</span><span class="sxs-lookup"><span data-stu-id="113c6-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="113c6-278">Атрибут `Range` ограничивает диапазон значений.</span><span class="sxs-lookup"><span data-stu-id="113c6-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="113c6-279">Атрибут `StringLength` задает максимальную и при необходимости минимальную длину строкового свойства.</span><span class="sxs-lookup"><span data-stu-id="113c6-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="113c6-280">Типы значений (например, `decimal`, `int`, `float`, `DateTime`) по своей природе являются обязательными и не требуют атрибута `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="113c6-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="113c6-281">На странице создания для модели `Movie` отображаются ошибки с недопустимыми значениями:</span><span class="sxs-lookup"><span data-stu-id="113c6-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Форма просмотра фильма с несколькими ошибками проверки jQuery на стороне клиента](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="113c6-283">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="113c6-283">For more information, see:</span></span>

* [<span data-ttu-id="113c6-284">Добавление проверки в приложение Movie</span><span class="sxs-lookup"><span data-stu-id="113c6-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="113c6-285">[Проверка модели в ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="113c6-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="113c6-286">Обработка запросов HEAD с помощью вызова резервного обработчика OnGet</span><span class="sxs-lookup"><span data-stu-id="113c6-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="113c6-287">Запросы `HEAD` позволяют получать заголовки для определенного ресурса.</span><span class="sxs-lookup"><span data-stu-id="113c6-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="113c6-288">В отличие от запросов `GET` запросы `HEAD`не возвращают текст ответа.</span><span class="sxs-lookup"><span data-stu-id="113c6-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="113c6-289">Обработчик `OnHead` обычно создается и вызывается для выполнения запросов `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="113c6-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="113c6-290">Если обработчик `OnHead` не определен, Razor Pages выполнит вызов обработчика `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="113c6-290">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="113c6-291">XSRF/CSRF и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="113c6-291">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="113c6-292">В Razor Pages реализована [проверка для защиты от подделки](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="113c6-292">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="113c6-293">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) вставляет маркеры защиты от подделки в элементы HTML-форм.</span><span class="sxs-lookup"><span data-stu-id="113c6-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="113c6-294">Использование макетов, частичных реплик, шаблонов и вспомогательных функций тегов с Razor Pages</span><span class="sxs-lookup"><span data-stu-id="113c6-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="113c6-295">Pages работает со всеми функциями подсистемы просмотра Razor.</span><span class="sxs-lookup"><span data-stu-id="113c6-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="113c6-296">Макеты, частичные реплики, шаблоны, вспомогательные функции тегов, а также файлы *_ViewStart.cshtml* и *_ViewImports.cshtml* работают точно так же, как и в стандартных представлениях Razor.</span><span class="sxs-lookup"><span data-stu-id="113c6-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="113c6-297">Давайте упростим нашу страницу с помощью некоторых из этих функций.</span><span class="sxs-lookup"><span data-stu-id="113c6-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="113c6-298">Добавим [макет страницы](xref:mvc/views/layout) в файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="113c6-299">Этот [макет](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="113c6-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="113c6-300">управляет макетом каждой страницы (кроме страниц с отказом от макета);</span><span class="sxs-lookup"><span data-stu-id="113c6-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="113c6-301">импортирует HTML-структуры, такие как JavaScript и таблицы стилей.</span><span class="sxs-lookup"><span data-stu-id="113c6-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="113c6-302">Содержимое страницы Razor отображается в том месте, где вызывается `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="113c6-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="113c6-303">Дополнительные сведения см. [здесь](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="113c6-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="113c6-304">Свойство [Layout](xref:mvc/views/layout#specifying-a-layout) определяется в файле *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="113c6-305">Макет хранится в папке *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="113c6-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="113c6-306">Pages ищет другие представления (макеты, шаблоны, частичные реплики) в иерархическом порядке, начиная с той папки, где находится текущая страница.</span><span class="sxs-lookup"><span data-stu-id="113c6-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="113c6-307">Макет в папке *Pages/Shared* можно использовать на любой странице Razor, которая находится в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="113c6-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="113c6-308">Файл макета следует поместить в папку *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="113c6-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="113c6-309">Корпорация Майкрософт рекомендует **не** размещать файл макета в папке *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="113c6-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="113c6-310">*Views/Shared* — это шаблон представлений MVC.</span><span class="sxs-lookup"><span data-stu-id="113c6-310">*Views/Shared* is an MVC views pattern.</span></span> Razor<span data-ttu-id="113c6-311"> Pages опирается на иерархию папок, а не на условные обозначения путей.</span><span class="sxs-lookup"><span data-stu-id="113c6-311"> Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="113c6-312">Поиск представлений в Razor Pages охватывает папку *Pages*.</span><span class="sxs-lookup"><span data-stu-id="113c6-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="113c6-313">Макеты, шаблоны и частичные реплики *работают* с контроллерами MVC и стандартными представлениями Razor.</span><span class="sxs-lookup"><span data-stu-id="113c6-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="113c6-314">Добавим файл *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="113c6-315">`@namespace` описывается далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="113c6-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="113c6-316">Директива `@addTagHelper` добавляет [встроенные вспомогательные теги](xref:mvc/views/tag-helpers/builtin-th/Index) на все страницы в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="113c6-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="113c6-317">Директива `@namespace`, заданная на странице:</span><span class="sxs-lookup"><span data-stu-id="113c6-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="113c6-318">Директива `@namespace` задает пространство имен для страницы.</span><span class="sxs-lookup"><span data-stu-id="113c6-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="113c6-319">Включать пространство имен в директиву `@model` не требуется.</span><span class="sxs-lookup"><span data-stu-id="113c6-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="113c6-320">Если директива `@namespace` содержится в файле *_ViewImports.cshtml*, указанное пространство имен определяет префикс для созданного в Pages пространства имен, куда импортируется директива `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="113c6-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="113c6-321">Остальная часть созданного пространства имен (суффикс) представляет собой разделенный точками относительный путь между папкой с файлом *_ViewImports.cshtml* и папкой, содержащей страницу.</span><span class="sxs-lookup"><span data-stu-id="113c6-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="113c6-322">Например, класс `PageModel` в файле *Pages/Customers/Edit.cshtml.cs* задает пространство имен явно.</span><span class="sxs-lookup"><span data-stu-id="113c6-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="113c6-323">Файл *Pages/_ViewImports.cshtml* задает следующее пространство имен:</span><span class="sxs-lookup"><span data-stu-id="113c6-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="113c6-324">Сформированное пространство имен для файла *Pages/Customers/Edit.cshtml* Razor Pages совпадает с пространством имен класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="113c6-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="113c6-325">`@namespace` *также работает со стандартными представлениями Razor.*</span><span class="sxs-lookup"><span data-stu-id="113c6-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="113c6-326">Рассмотрим файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="113c6-327">Обновленный файл представления *Pages/Create.cshtml* с *_ViewImports.cshtml* и предыдущим файлом макета:</span><span class="sxs-lookup"><span data-stu-id="113c6-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="113c6-328">В приведенном выше коде *_ViewImports. cshtml* импортировал пространство имен и вспомогательные функции тегов.</span><span class="sxs-lookup"><span data-stu-id="113c6-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="113c6-329">Файл макета импортировал файлы JavaScript.</span><span class="sxs-lookup"><span data-stu-id="113c6-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="113c6-330">[Начальный проект Razor Pages](#rpvs17) содержит файл *Pages/_ValidationScriptsPartial.cshtml*, который подключает проверку на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="113c6-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="113c6-331">Дополнительные сведения о частичных представлениях см. в <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="113c6-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="113c6-332">Формирование URL-адресов для страниц</span><span class="sxs-lookup"><span data-stu-id="113c6-332">URL generation for Pages</span></span>

<span data-ttu-id="113c6-333">На представленной выше странице `Create` используется `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="113c6-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="113c6-334">Это приложение имеет следующую структуру файлов и папок:</span><span class="sxs-lookup"><span data-stu-id="113c6-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="113c6-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="113c6-335">*/Pages*</span></span>

  * <span data-ttu-id="113c6-336">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="113c6-337">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="113c6-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="113c6-338">*/Customers*</span></span>

    * <span data-ttu-id="113c6-339">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="113c6-340">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="113c6-341">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-341">*Index.cshtml*</span></span>

<span data-ttu-id="113c6-342">После успешного выполнения страницы *Pages/Customers/Create.cshtml* и *Pages/Customers/Edit.cshtml* перенаправляются на страницу *Pages/Customers/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="113c6-343">Строка `./Index` — это относительное имя страницы, используемое для доступа к предыдущей странице.</span><span class="sxs-lookup"><span data-stu-id="113c6-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="113c6-344">Эта строка позволяет создавать URL-адреса для страницы *Pages/Customers/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="113c6-345">Пример:</span><span class="sxs-lookup"><span data-stu-id="113c6-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="113c6-346">С помощью абсолютного имени страницы `/Index` создаются URL-адреса страницы *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="113c6-347">Пример:</span><span class="sxs-lookup"><span data-stu-id="113c6-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="113c6-348">Имя страницы — это путь к странице из корневой папки */Pages*, включая начальный символ `/` (например, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="113c6-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="113c6-349">Предыдущие образцы создания URL-адреса обеспечивают расширенные параметры и функциональные возможности по сравнению с жестким заданием URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="113c6-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="113c6-350">Формирование URL-адресов включает [маршрутизацию](xref:mvc/controllers/routing) и позволяет генерировать и включать в код параметры в зависимости от того, как определяется маршрут в пути назначения.</span><span class="sxs-lookup"><span data-stu-id="113c6-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="113c6-351">Формирование URL-адресов для страниц поддерживает относительные имена.</span><span class="sxs-lookup"><span data-stu-id="113c6-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="113c6-352">В приведенной ниже таблице показано, какая страница индекса выбирается с помощью разных параметров `RedirectToPage` в *Pages/Customers/Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="113c6-353">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="113c6-353">RedirectToPage(x)</span></span>| <span data-ttu-id="113c6-354">Страница</span><span class="sxs-lookup"><span data-stu-id="113c6-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="113c6-355">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="113c6-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="113c6-356">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="113c6-356">*Pages/Index*</span></span> |
| <span data-ttu-id="113c6-357">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="113c6-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="113c6-358">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="113c6-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="113c6-359">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="113c6-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="113c6-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="113c6-360">*Pages/Index*</span></span> |
| <span data-ttu-id="113c6-361">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="113c6-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="113c6-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="113c6-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="113c6-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")` и `RedirectToPage("../Index")` — это *относительные имена*.</span><span class="sxs-lookup"><span data-stu-id="113c6-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="113c6-364">Для получения имени целевой страницы параметр `RedirectToPage`*комбинируется* с путем текущей страницы.</span><span class="sxs-lookup"><span data-stu-id="113c6-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="113c6-365">Привязка относительных имен полезна при создании сайтов со сложной структурой.</span><span class="sxs-lookup"><span data-stu-id="113c6-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="113c6-366">Если относительные имена используются для связи между страницами в папке:</span><span class="sxs-lookup"><span data-stu-id="113c6-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="113c6-367">Переименование папки не нарушает относительные ссылки.</span><span class="sxs-lookup"><span data-stu-id="113c6-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="113c6-368">Ссылки не нарушаются, так как не содержат имя папки.</span><span class="sxs-lookup"><span data-stu-id="113c6-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="113c6-369">Чтобы выполнить перенаправление на страницу в другой [области](xref:mvc/controllers/areas), укажите эту область:</span><span class="sxs-lookup"><span data-stu-id="113c6-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="113c6-370">Дополнительные сведения см. в разделах <xref:mvc/controllers/areas> и <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="113c6-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="113c6-371">Атрибут ViewData</span><span class="sxs-lookup"><span data-stu-id="113c6-371">ViewData attribute</span></span>

<span data-ttu-id="113c6-372">Данные могут передаваться на страницу с помощью атрибута <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span><span class="sxs-lookup"><span data-stu-id="113c6-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="113c6-373">Значения свойств с атрибутом `[ViewData]` хранятся в <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> и загружаются из него.</span><span class="sxs-lookup"><span data-stu-id="113c6-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="113c6-374">В следующем примере `AboutModel` применяет атрибут `[ViewData]` к свойству `Title`:</span><span class="sxs-lookup"><span data-stu-id="113c6-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="113c6-375">На странице About доступ к свойству `Title` осуществляется как доступ к свойству модели.</span><span class="sxs-lookup"><span data-stu-id="113c6-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="113c6-376">В макете заголовок считывается из словаря ViewData.</span><span class="sxs-lookup"><span data-stu-id="113c6-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="113c6-377">TempData</span><span class="sxs-lookup"><span data-stu-id="113c6-377">TempData</span></span>

<span data-ttu-id="113c6-378">ASP.NET Core предоставляет <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span><span class="sxs-lookup"><span data-stu-id="113c6-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="113c6-379">Это свойство хранит данные до тех пор, пока они не будут прочитаны.</span><span class="sxs-lookup"><span data-stu-id="113c6-379">This property stores data until it's read.</span></span> <span data-ttu-id="113c6-380">Для проверки данных без удаления можно использовать методы <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> и <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*>.</span><span class="sxs-lookup"><span data-stu-id="113c6-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="113c6-381">`TempData` удобно использовать для перенаправления, когда данные требуются больше, чем для одного запроса.</span><span class="sxs-lookup"><span data-stu-id="113c6-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="113c6-382">В следующем коде значение `Message` задается с помощью `TempData`:</span><span class="sxs-lookup"><span data-stu-id="113c6-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="113c6-383">Представленная ниже разметка в файле *Pages/Customers/Index.cshtml* отображает значение `Message` с помощью `TempData`.</span><span class="sxs-lookup"><span data-stu-id="113c6-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="113c6-384">Модель страницы *Pages/Customers/Index.cshtml.cs* применяет атрибут `[TempData]` к свойству `Message`.</span><span class="sxs-lookup"><span data-stu-id="113c6-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="113c6-385">Дополнительные сведения см. в разделе [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="113c6-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="113c6-386">Несколько обработчиков на страницу</span><span class="sxs-lookup"><span data-stu-id="113c6-386">Multiple handlers per page</span></span>

<span data-ttu-id="113c6-387">Следующая страница формирует разметку для двух обработчиков с помощью вспомогательной функции тегов `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="113c6-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="113c6-388">Форма в предыдущем примере включает две кнопки отправки, каждая из которых отправляет данные на отдельный URL-адрес с помощью `FormActionTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="113c6-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="113c6-389">Атрибут `asp-page-handler` является дополнением к `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="113c6-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="113c6-390">Атрибут `asp-page-handler` формирует URL-адреса, ,которые используются для отправки данных в каждый из методов обработчиков, определенных страницей.</span><span class="sxs-lookup"><span data-stu-id="113c6-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="113c6-391">`asp-page` не задается, так как пример сопоставлен с текущей страницей.</span><span class="sxs-lookup"><span data-stu-id="113c6-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="113c6-392">Модель страницы</span><span class="sxs-lookup"><span data-stu-id="113c6-392">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="113c6-393">В представленном выше коде используются *именованные методы обработчика*.</span><span class="sxs-lookup"><span data-stu-id="113c6-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="113c6-394">Именованные методы обработчика создаются путем размещения определенного текста в имени после `On<HTTP Verb>` и перед `Async` (если есть).</span><span class="sxs-lookup"><span data-stu-id="113c6-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="113c6-395">В приведенном выше примере использовались методы страницы OnPost**JoinList**Async и OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="113c6-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="113c6-396">Если убрать *OnPost* и *Async*, имена обработчиков будут выглядеть как `JoinList` и `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="113c6-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="113c6-397">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="113c6-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="113c6-398">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="113c6-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="113c6-399">Пользовательские маршруты</span><span class="sxs-lookup"><span data-stu-id="113c6-399">Custom routes</span></span>

<span data-ttu-id="113c6-400">С помощью директивы `@page` можно сделать следующее.</span><span class="sxs-lookup"><span data-stu-id="113c6-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="113c6-401">Указать пользовательский маршрут к странице.</span><span class="sxs-lookup"><span data-stu-id="113c6-401">Specify a custom route to a page.</span></span> <span data-ttu-id="113c6-402">Например, можно задать маршрут к странице "Сведения" `/Some/Other/Path`: `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="113c6-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="113c6-403">Добавить сегменты к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="113c6-403">Append segments to a page's default route.</span></span> <span data-ttu-id="113c6-404">Например, к такому маршруту можно добавить сегмент item: `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="113c6-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="113c6-405">Добавить параметры к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="113c6-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="113c6-406">Например, для страницы с `@page "{id}"` может потребоваться параметр идентификатора `id`.</span><span class="sxs-lookup"><span data-stu-id="113c6-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="113c6-407">Поддерживается путь относительно корня, заданный знаком тильды (`~`) в начале пути.</span><span class="sxs-lookup"><span data-stu-id="113c6-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="113c6-408">Например, `@page "~/Some/Other/Path"` равносильно `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="113c6-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="113c6-409">Если вы не хотите, чтобы в URL-адресе отображалась строка запроса `?handler=JoinList`, измените маршрут так, чтобы в качестве пути в URL-адресе указывалось имя обработчика.</span><span class="sxs-lookup"><span data-stu-id="113c6-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="113c6-410">Для настройки маршрута добавьте после директивы `@page` шаблон маршрута, заключенный в двойные кавычки.</span><span class="sxs-lookup"><span data-stu-id="113c6-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="113c6-411">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="113c6-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="113c6-412">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="113c6-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="113c6-413">Символ `?` после `handler` означает, что параметр маршрута является необязательным.</span><span class="sxs-lookup"><span data-stu-id="113c6-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="113c6-414">Расширенная конфигурация и параметры</span><span class="sxs-lookup"><span data-stu-id="113c6-414">Advanced configuration and settings</span></span>

<span data-ttu-id="113c6-415">Конфигурация и параметры в следующих разделах не требуются большинству приложений.</span><span class="sxs-lookup"><span data-stu-id="113c6-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="113c6-416">Чтобы настроить дополнительные параметры, воспользуйтесь методом расширения <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span><span class="sxs-lookup"><span data-stu-id="113c6-416">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="113c6-417">Используйте <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>, чтобы задавать корневой каталог для страниц и добавлять для них соглашения моделей приложений.</span><span class="sxs-lookup"><span data-stu-id="113c6-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="113c6-418">Дополнительные сведения о соглашениях см. в разделе [Соглашения об авторизации Razor Pages](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="113c6-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="113c6-419">Сведения о предварительной компиляции представлений см. на странице [Компиляция представлений Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="113c6-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="113c6-420">Указание местонахождения Razor Pages в корне каталога</span><span class="sxs-lookup"><span data-stu-id="113c6-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="113c6-421">По умолчанию Razor Pages находится в корне каталога */Pages*.</span><span class="sxs-lookup"><span data-stu-id="113c6-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="113c6-422">Добавьте <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*>, чтобы указать, что Razor Pages находится в [корневой папке содержимого](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) приложения:</span><span class="sxs-lookup"><span data-stu-id="113c6-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="113c6-423">Указание местонахождения Razor Pages в пользовательском корневом каталоге</span><span class="sxs-lookup"><span data-stu-id="113c6-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="113c6-424">Добавьте <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*>, чтобы указать, что Razor Pages находится в пользовательском корневом каталоге в приложении (укажите относительный путь):</span><span class="sxs-lookup"><span data-stu-id="113c6-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="113c6-425">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="113c6-425">Additional resources</span></span>

* <span data-ttu-id="113c6-426">Дополнительные общие сведения см. на странице [Учебник. Начало работы с Razor Pages в ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="113c6-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* <span data-ttu-id="113c6-427">[Атрибут authorize и Razor Pages](xref:security/authorization/simple#aarp)</span><span class="sxs-lookup"><span data-stu-id="113c6-427">[Authorize attribute and Razor Pages](xref:security/authorization/simple#aarp)</span></span>
* <span data-ttu-id="113c6-428">[Загрузить или просмотреть пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample).</span><span class="sxs-lookup"><span data-stu-id="113c6-428">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)</span></span>
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="113c6-429">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Райан Новак](https://github.com/rynowak) (Ryan Nowak)</span><span class="sxs-lookup"><span data-stu-id="113c6-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

Razor<span data-ttu-id="113c6-430"> Pages — это новый аспект платформы MVC ASP.NET Core, который делает создание кодов сценариев для страниц проще и эффективнее.</span><span class="sxs-lookup"><span data-stu-id="113c6-430"> Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="113c6-431">Если вам нужно руководство, использующее подход "модель-представление-контроллер", см. статью [Начало работы с MVC в ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="113c6-431">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="113c6-432">Этот документ содержит вводные сведения о Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="113c6-432">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="113c6-433">Это не пошаговое руководство.</span><span class="sxs-lookup"><span data-stu-id="113c6-433">It's not a step by step tutorial.</span></span> <span data-ttu-id="113c6-434">Если некоторые разделы покажутся вам слишком сложными, см. [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="113c6-434">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="113c6-435">Общие сведения об ASP.NET Core см. в разделе [Введение в ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="113c6-435">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="113c6-436">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="113c6-436">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="113c6-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="113c6-437">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="113c6-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="113c6-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="113c6-439">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="113c6-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="113c6-440">Создание проекта Razor Pages</span><span class="sxs-lookup"><span data-stu-id="113c6-440">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="113c6-441">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="113c6-441">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="113c6-442">Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="113c6-442">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="113c6-443">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="113c6-443">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="113c6-444">Из командной строки выполните команду `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="113c6-444">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="113c6-445">Откройте созданный файл *.csproj* в Visual Studio для Mac.</span><span class="sxs-lookup"><span data-stu-id="113c6-445">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="113c6-446">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="113c6-446">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="113c6-447">Из командной строки выполните команду `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="113c6-447">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a>Razor<span data-ttu-id="113c6-448"> Pages</span><span class="sxs-lookup"><span data-stu-id="113c6-448"> Pages</span></span>

Razor<span data-ttu-id="113c6-449"> Pages активируется в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="113c6-449"> Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="113c6-450">Рассмотрим простую страницу: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="113c6-450">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="113c6-451">Приведенный выше код выглядит как [файл представления Razor](xref:tutorials/first-mvc-app/adding-view), используемый в приложениях ASP.NET Core с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="113c6-451">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="113c6-452">и отличается от него только директивой `@page`.</span><span class="sxs-lookup"><span data-stu-id="113c6-452">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="113c6-453">Директива `@page` превращает файл в действие MVC, а значит обрабатывает запросы напрямую, минуя контроллер.</span><span class="sxs-lookup"><span data-stu-id="113c6-453">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="113c6-454">`@page` должна быть первой директивой Razor на странице.</span><span class="sxs-lookup"><span data-stu-id="113c6-454">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="113c6-455">`@page` влияет на поведение всех остальных конструкций Razor.</span><span class="sxs-lookup"><span data-stu-id="113c6-455">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="113c6-456">Похожая страница с использованием класса `PageModel` показана в следующих двух файлах.</span><span class="sxs-lookup"><span data-stu-id="113c6-456">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="113c6-457">Файл *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-457">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="113c6-458">Модель страницы *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="113c6-458">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="113c6-459">Как правило, файл класса `PageModel` называется так же, как файл Razor Pages, но с расширением *CS*.</span><span class="sxs-lookup"><span data-stu-id="113c6-459">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="113c6-460">Например, представленная выше страница Razor Pages называется *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-460">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="113c6-461">Файл, содержащий класс `PageModel`, называется *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="113c6-461">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="113c6-462">Сопоставления URL-адресов со страницами определяются расположением конкретной страницы в файловой системе.</span><span class="sxs-lookup"><span data-stu-id="113c6-462">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="113c6-463">В приведенной ниже таблице показаны пути Razor Pages и соответствующие URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="113c6-463">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="113c6-464">Имя файла и путь</span><span class="sxs-lookup"><span data-stu-id="113c6-464">File name and path</span></span>               | <span data-ttu-id="113c6-465">Соответствующий URL</span><span class="sxs-lookup"><span data-stu-id="113c6-465">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="113c6-466">*/Pages/index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-466">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="113c6-467">`/` или `/Index`</span><span class="sxs-lookup"><span data-stu-id="113c6-467">`/` or `/Index`</span></span> |
| <span data-ttu-id="113c6-468">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-468">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="113c6-469">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-469">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="113c6-470">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-470">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="113c6-471">`/Store` или `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="113c6-471">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="113c6-472">Примечания.</span><span class="sxs-lookup"><span data-stu-id="113c6-472">Notes:</span></span>

* <span data-ttu-id="113c6-473">Среда выполнения по умолчанию ищет файлы Razor Pages в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="113c6-473">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="113c6-474">Если в URL-адресе не указана конкретная страница, по умолчанию открывается страница `Index`.</span><span class="sxs-lookup"><span data-stu-id="113c6-474">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="113c6-475">Создание простой формы</span><span class="sxs-lookup"><span data-stu-id="113c6-475">Write a basic form</span></span>

Razor<span data-ttu-id="113c6-476"> Pages предназначена для упрощения реализации типовых шаблонов, которые используются в браузерах, при создании приложения.</span><span class="sxs-lookup"><span data-stu-id="113c6-476"> Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="113c6-477">[Привязки модели](xref:mvc/models/model-binding), [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и вспомогательные методы HTML *отлично работают* со свойствами, определенными в классе Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="113c6-477">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="113c6-478">Рассмотрим страницу с простой формой связи для модели `Contact`.</span><span class="sxs-lookup"><span data-stu-id="113c6-478">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="113c6-479">В представленных в этой статье примерах `DbContext` инициализируется в файле [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="113c6-479">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="113c6-480">Модель данных:</span><span class="sxs-lookup"><span data-stu-id="113c6-480">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="113c6-481">Контекст базы данных:</span><span class="sxs-lookup"><span data-stu-id="113c6-481">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="113c6-482">Файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-482">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="113c6-483">Модель страницы *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="113c6-483">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="113c6-484">Как правило, класс `PageModel` называется `<PageName>Model` и находится в том же пространстве имен, что и страница.</span><span class="sxs-lookup"><span data-stu-id="113c6-484">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="113c6-485">Класс `PageModel` позволяет разделять логику страницы и ее представление.</span><span class="sxs-lookup"><span data-stu-id="113c6-485">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="113c6-486">Он определяет обработчики страницы для запросов, отправляемых на страницу, а также данные для ее визуализации.</span><span class="sxs-lookup"><span data-stu-id="113c6-486">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="113c6-487">Это разделение позволяет:</span><span class="sxs-lookup"><span data-stu-id="113c6-487">This separation allows:</span></span>

* <span data-ttu-id="113c6-488">управлять зависимостями страниц с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="113c6-488">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="113c6-489">[Модульное тестирование](xref:test/razor-pages-tests) страниц.</span><span class="sxs-lookup"><span data-stu-id="113c6-489">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="113c6-490">Страница содержит *метод обработчика* `OnPostAsync`, который выполняется по запросам `POST` (когда пользователь публикует форму).</span><span class="sxs-lookup"><span data-stu-id="113c6-490">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="113c6-491">Методы обработчика можно добавить для любой HTTP-команды.</span><span class="sxs-lookup"><span data-stu-id="113c6-491">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="113c6-492">Наиболее распространенные обработчики</span><span class="sxs-lookup"><span data-stu-id="113c6-492">The most common handlers are:</span></span>

* <span data-ttu-id="113c6-493">`OnGet` — инициализация необходимого для страницы состояния.</span><span class="sxs-lookup"><span data-stu-id="113c6-493">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="113c6-494">Пример обработчика [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="113c6-494">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="113c6-495">`OnPost` — обработка отправленных через форму данных.</span><span class="sxs-lookup"><span data-stu-id="113c6-495">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="113c6-496">Суффикс `Async` не является обязательным, но часто используется для асинхронных функций.</span><span class="sxs-lookup"><span data-stu-id="113c6-496">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="113c6-497">Этот код типичен для Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="113c6-497">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="113c6-498">Если вы знакомы с приложениями ASP.NET, использующими контроллеры и представления:</span><span class="sxs-lookup"><span data-stu-id="113c6-498">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="113c6-499">Код `OnPostAsync` в предыдущем примере похож на стандартный код контроллера.</span><span class="sxs-lookup"><span data-stu-id="113c6-499">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="113c6-500">Большинство примитивов MVC, включая [привязку модели](xref:mvc/models/model-binding), [проверку](xref:mvc/models/validation), [проверку](xref:mvc/models/validation) и результаты действий, являются общими.</span><span class="sxs-lookup"><span data-stu-id="113c6-500">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="113c6-501">Предыдущий метод `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="113c6-501">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="113c6-502">Простая схема `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="113c6-502">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="113c6-503">Проверка на наличие ошибок проверки.</span><span class="sxs-lookup"><span data-stu-id="113c6-503">Check for validation errors.</span></span>

* <span data-ttu-id="113c6-504">Если ошибок нет, сохранение данных и перенаправление.</span><span class="sxs-lookup"><span data-stu-id="113c6-504">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="113c6-505">Если есть ошибки, отображение страницы с сообщениями проверки.</span><span class="sxs-lookup"><span data-stu-id="113c6-505">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="113c6-506">Проверка на стороне клиента выполняется точно так же, как в традиционных приложениях MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="113c6-506">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="113c6-507">Во многих случаях ошибки проверки выявляются на клиентском компьютере и на сервер не отправляются.</span><span class="sxs-lookup"><span data-stu-id="113c6-507">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="113c6-508">После успешного ввода данных метод обработчика `OnPostAsync` вызывает метод обработчика `RedirectToPage`, чтобы получить экземпляр `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="113c6-508">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="113c6-509">`RedirectToPage` — это новый результат действия, аналогичный `RedirectToAction` или `RedirectToRoute`, но настроенный для страниц.</span><span class="sxs-lookup"><span data-stu-id="113c6-509">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="113c6-510">В приведенном выше примере он выполняет перенаправление на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="113c6-510">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="113c6-511">Более подробно `RedirectToPage` рассматривается в разделе [Создание URL для страниц](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="113c6-511">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="113c6-512">Если в отправленной форме имеются ошибки проверки (переданные на сервер), метод обработчика `OnPostAsync` вызывает метод обработчика `Page`.</span><span class="sxs-lookup"><span data-stu-id="113c6-512">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="113c6-513">`Page` возвращает экземпляр `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="113c6-513">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="113c6-514">Возвращение `Page` аналогично тому, как действия в контроллерах возвращают `View`.</span><span class="sxs-lookup"><span data-stu-id="113c6-514">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="113c6-515">`PageResult` — тип возвращаемого значения по умолчанию для метода обработчика.</span><span class="sxs-lookup"><span data-stu-id="113c6-515">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="113c6-516">Метод обработчика, вернувший `void`, визуализирует страницу.</span><span class="sxs-lookup"><span data-stu-id="113c6-516">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="113c6-517">Для указания согласия на привязку модели в свойстве `Customer` используется атрибут `[BindProperty]`.</span><span class="sxs-lookup"><span data-stu-id="113c6-517">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="113c6-518">По умолчанию Razor Pages привязывает свойства ко всем командам, кроме `GET`.</span><span class="sxs-lookup"><span data-stu-id="113c6-518">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="113c6-519">Привязка к свойствам позволяет сократить объем необходимого кода.</span><span class="sxs-lookup"><span data-stu-id="113c6-519">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="113c6-520">Привязка уменьшает код за счет того, что для визуализации полей формы (`<input asp-for="Customer.Name">`) и получения входных данных используется одно и то же свойство.</span><span class="sxs-lookup"><span data-stu-id="113c6-520">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="113c6-521">Домашняя страница (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="113c6-521">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="113c6-522">Связанный класс `PageModel` (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="113c6-522">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="113c6-523">Файл *Index.cshtml* содержит следующую разметку для создания ссылки на правку для каждого контактного лица.</span><span class="sxs-lookup"><span data-stu-id="113c6-523">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="113c6-524">[Вспомогательный тег привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` использовал атрибут `asp-route-{value}` для создания ссылки на страницу редактирования.</span><span class="sxs-lookup"><span data-stu-id="113c6-524">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="113c6-525">Эта ссылка содержит данные о маршруте с идентификатором контактного лица.</span><span class="sxs-lookup"><span data-stu-id="113c6-525">The link contains route data with the contact ID.</span></span> <span data-ttu-id="113c6-526">Например, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="113c6-526">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="113c6-527">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="113c6-527">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="113c6-528">Вспомогательные функции тегов включены с помощью `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="113c6-528">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="113c6-529">Файл *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-529">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="113c6-530">Первая строка содержит директиву `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="113c6-530">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="113c6-531">Ограничение маршрутизации `"{id:int}"` указывает, что страница должна принимать обращенные к ней запросы, которые содержат данные о маршруте `int`.</span><span class="sxs-lookup"><span data-stu-id="113c6-531">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="113c6-532">Если запрос к странице не содержит данные о маршруте, которые можно конвертировать в `int`, среда выполнения возвращает ошибку HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="113c6-532">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="113c6-533">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="113c6-533">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="113c6-534">Файл *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="113c6-534">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="113c6-535">Файл *Index.cshtml* также содержит разметку для создания кнопки удаления у каждого контакта клиента:</span><span class="sxs-lookup"><span data-stu-id="113c6-535">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="113c6-536">Во время обработки кнопки удаления в HTML ее `formaction` включает параметры для следующего:</span><span class="sxs-lookup"><span data-stu-id="113c6-536">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="113c6-537">Идентификатор контакта клиента, указанный атрибутом `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="113c6-537">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="113c6-538">Параметр `handler`, указанный атрибутом `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="113c6-538">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="113c6-539">Пример отображенной кнопки удаления с идентификатором контакта клиента `1`:</span><span class="sxs-lookup"><span data-stu-id="113c6-539">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="113c6-540">При выборе кнопки на сервер отправляется запрос формы `POST`.</span><span class="sxs-lookup"><span data-stu-id="113c6-540">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="113c6-541">По соглашению имя метода обработчика выбирается на основе значения параметра `handler` в соответствии со схемой `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="113c6-541">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="113c6-542">Так как `handler` — `delete` в этом примере, метод обработчика `OnPostDeleteAsync` используется для обработки запроса `POST`.</span><span class="sxs-lookup"><span data-stu-id="113c6-542">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="113c6-543">Если `asp-page-handler` имеет другое значение, например `remove`, выбирается метод обработчика с именем `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="113c6-543">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="113c6-544">В приведенном ниже коде показан обработчик `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="113c6-544">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="113c6-545">Метод `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="113c6-545">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="113c6-546">Принимает `id` из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="113c6-546">Accepts the `id` from the query string.</span></span> <span data-ttu-id="113c6-547">Если директива страницы *index.cshtml* содержит ограничение маршрутизации `"{id:int?}"`, то `id` будет получено из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="113c6-547">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="113c6-548">Данные маршрута для `id` указываются в универсальном коде ресурса (URI), например `https://localhost:5001/Customers/2`.</span><span class="sxs-lookup"><span data-stu-id="113c6-548">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="113c6-549">Отправляет в базу данных запрос контакта клиента с `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="113c6-549">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="113c6-550">Если контакт клиента найден, он удаляется из списка контактов.</span><span class="sxs-lookup"><span data-stu-id="113c6-550">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="113c6-551">База данных обновляется.</span><span class="sxs-lookup"><span data-stu-id="113c6-551">The database is updated.</span></span>
* <span data-ttu-id="113c6-552">Вызывает `RedirectToPage` для перенаправления на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="113c6-552">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="113c6-553">Маркировка свойств страницы как обязательных</span><span class="sxs-lookup"><span data-stu-id="113c6-553">Mark page properties as required</span></span>

<span data-ttu-id="113c6-554">Свойства класса `PageModel` можно отметить атрибутом [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute):</span><span class="sxs-lookup"><span data-stu-id="113c6-554">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="113c6-555">Дополнительные сведения см. в статье [Проверка модели](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="113c6-555">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="113c6-556">Обработка запросов HEAD с помощью вызова резервного обработчика OnGet</span><span class="sxs-lookup"><span data-stu-id="113c6-556">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="113c6-557">Запросы `HEAD` позволяют получать заголовки для определенного ресурса.</span><span class="sxs-lookup"><span data-stu-id="113c6-557">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="113c6-558">В отличие от запросов `GET` запросы `HEAD`не возвращают текст ответа.</span><span class="sxs-lookup"><span data-stu-id="113c6-558">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="113c6-559">Обработчик `OnHead` обычно создается и вызывается для выполнения запросов `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="113c6-559">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="113c6-560">Если в ASP.NET Core 2.1 или более поздней версии обработчик `OnHead` не определен, Razor Pages выполнит вызов резервного обработчика `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="113c6-560">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="113c6-561">Это поведение включается путем вызова [SetCompatibilityVersion](xref:mvc/compatibility-version) в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="113c6-561">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="113c6-562">Шаблоны по умолчанию создают вызов `SetCompatibilityVersion` в ASP.NET Core 2.1 и 2.2.</span><span class="sxs-lookup"><span data-stu-id="113c6-562">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="113c6-563">`SetCompatibilityVersion` задает для параметра `AllowMappingHeadRequestsToGetHandler` Razor Pages значение `true`.</span><span class="sxs-lookup"><span data-stu-id="113c6-563">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="113c6-564">Вместо включения всех возможных поведений с помощью метода `SetCompatibilityVersion` вы можете задать *конкретное* поведение.</span><span class="sxs-lookup"><span data-stu-id="113c6-564">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="113c6-565">Следующий код позволяет сопоставлять запросы `HEAD` с обработчиком `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="113c6-565">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="113c6-566">XSRF/CSRF и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="113c6-566">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="113c6-567">Вам не придется писать отдельный код для [проверки подлинности запросов](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="113c6-567">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="113c6-568">Razor Pages включает создание и проверку маркеров защиты от подделок по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="113c6-568">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="113c6-569">Использование макетов, частичных реплик, шаблонов и вспомогательных функций тегов с Razor Pages</span><span class="sxs-lookup"><span data-stu-id="113c6-569">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="113c6-570">Pages работает со всеми функциями подсистемы просмотра Razor.</span><span class="sxs-lookup"><span data-stu-id="113c6-570">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="113c6-571">Макеты, частичные реплики, шаблоны, вспомогательные функции тегов, а также файлы *_ViewStart.cshtml* и *_ViewImports.cshtml* работают точно так же, как и в стандартных представлениях Razor.</span><span class="sxs-lookup"><span data-stu-id="113c6-571">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="113c6-572">Давайте упростим нашу страницу с помощью некоторых из этих функций.</span><span class="sxs-lookup"><span data-stu-id="113c6-572">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="113c6-573">Добавим [макет страницы](xref:mvc/views/layout) в файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-573">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="113c6-574">Этот [макет](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="113c6-574">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="113c6-575">управляет макетом каждой страницы (кроме страниц с отказом от макета);</span><span class="sxs-lookup"><span data-stu-id="113c6-575">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="113c6-576">импортирует HTML-структуры, такие как JavaScript и таблицы стилей.</span><span class="sxs-lookup"><span data-stu-id="113c6-576">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="113c6-577">Дополнительные сведения см. в статье о [макете](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="113c6-577">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="113c6-578">Свойство [Layout](xref:mvc/views/layout#specifying-a-layout) определяется в файле *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-578">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="113c6-579">Макет хранится в папке *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="113c6-579">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="113c6-580">Pages ищет другие представления (макеты, шаблоны, частичные реплики) в иерархическом порядке, начиная с той папки, где находится текущая страница.</span><span class="sxs-lookup"><span data-stu-id="113c6-580">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="113c6-581">Макет в папке *Pages/Shared* можно использовать на любой странице Razor, которая находится в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="113c6-581">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="113c6-582">Файл макета следует поместить в папку *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="113c6-582">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="113c6-583">Корпорация Майкрософт рекомендует **не** размещать файл макета в папке *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="113c6-583">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="113c6-584">*Views/Shared* — это шаблон представлений MVC.</span><span class="sxs-lookup"><span data-stu-id="113c6-584">*Views/Shared* is an MVC views pattern.</span></span> Razor<span data-ttu-id="113c6-585"> Pages опирается на иерархию папок, а не на условные обозначения путей.</span><span class="sxs-lookup"><span data-stu-id="113c6-585"> Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="113c6-586">Поиск представлений в Razor Pages охватывает папку *Pages*.</span><span class="sxs-lookup"><span data-stu-id="113c6-586">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="113c6-587">Макеты, шаблоны и частичные реплики *работают* с контроллерами MVC и стандартными представлениями Razor.</span><span class="sxs-lookup"><span data-stu-id="113c6-587">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="113c6-588">Добавим файл *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-588">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="113c6-589">`@namespace` описывается далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="113c6-589">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="113c6-590">Директива `@addTagHelper` добавляет [встроенные вспомогательные теги](xref:mvc/views/tag-helpers/builtin-th/Index) на все страницы в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="113c6-590">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="113c6-591">Явное применение директивы `@namespace` на странице:</span><span class="sxs-lookup"><span data-stu-id="113c6-591">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="113c6-592">Данная директива задает пространство имен для страницы.</span><span class="sxs-lookup"><span data-stu-id="113c6-592">The directive sets the namespace for the page.</span></span> <span data-ttu-id="113c6-593">Включать пространство имен в директиву `@model` не требуется.</span><span class="sxs-lookup"><span data-stu-id="113c6-593">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="113c6-594">Если директива `@namespace` содержится в файле *_ViewImports.cshtml*, указанное пространство имен определяет префикс для созданного в Pages пространства имен, куда импортируется директива `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="113c6-594">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="113c6-595">Остальная часть созданного пространства имен (суффикс) представляет собой разделенный точками относительный путь между папкой с файлом *_ViewImports.cshtml* и папкой, содержащей страницу.</span><span class="sxs-lookup"><span data-stu-id="113c6-595">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="113c6-596">Например, класс `PageModel` в файле *Pages/Customers/Edit.cshtml.cs* задает пространство имен явно.</span><span class="sxs-lookup"><span data-stu-id="113c6-596">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="113c6-597">Файл *Pages/_ViewImports.cshtml* задает следующее пространство имен:</span><span class="sxs-lookup"><span data-stu-id="113c6-597">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="113c6-598">Сформированное пространство имен для файла *Pages/Customers/Edit.cshtml* Razor Pages совпадает с пространством имен класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="113c6-598">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="113c6-599">`@namespace` *также работает со стандартными представлениями Razor.*</span><span class="sxs-lookup"><span data-stu-id="113c6-599">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="113c6-600">Исходный файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-600">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="113c6-601">Обновленный файл представления *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="113c6-601">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="113c6-602">[Начальный проект Razor Pages](#rpvs17) содержит файл *Pages/_ValidationScriptsPartial.cshtml*, который подключает проверку на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="113c6-602">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="113c6-603">Дополнительные сведения о частичных представлениях см. в <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="113c6-603">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="113c6-604">Формирование URL-адресов для страниц</span><span class="sxs-lookup"><span data-stu-id="113c6-604">URL generation for Pages</span></span>

<span data-ttu-id="113c6-605">На представленной выше странице `Create` используется `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="113c6-605">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="113c6-606">Это приложение имеет следующую структуру файлов и папок:</span><span class="sxs-lookup"><span data-stu-id="113c6-606">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="113c6-607">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="113c6-607">*/Pages*</span></span>

  * <span data-ttu-id="113c6-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-608">*Index.cshtml*</span></span>
  * <span data-ttu-id="113c6-609">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="113c6-609">*/Customers*</span></span>

    * <span data-ttu-id="113c6-610">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-610">*Create.cshtml*</span></span>
    * <span data-ttu-id="113c6-611">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-611">*Edit.cshtml*</span></span>
    * <span data-ttu-id="113c6-612">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="113c6-612">*Index.cshtml*</span></span>

<span data-ttu-id="113c6-613">После успешного выполнения страницы *Pages/Customers/Create.cshtml* и *Pages/Customers/Edit.cshtml* перенаправляются на страницу *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-613">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="113c6-614">Строка `/Index` составляет часть URL-адреса для доступа к указанной выше странице.</span><span class="sxs-lookup"><span data-stu-id="113c6-614">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="113c6-615">Строка `/Index` позволяет создавать URL-адреса для страницы *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-615">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="113c6-616">Пример:</span><span class="sxs-lookup"><span data-stu-id="113c6-616">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="113c6-617">Имя страницы — это путь к странице из корневой папки */Pages*, включая начальный символ `/` (например, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="113c6-617">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="113c6-618">Предыдущие образцы создания URL-адреса обеспечивают расширенные параметры и функциональные возможности по сравнению с жестким заданием URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="113c6-618">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="113c6-619">Формирование URL-адресов включает [маршрутизацию](xref:mvc/controllers/routing) и позволяет генерировать и включать в код параметры в зависимости от того, как определяется маршрут в пути назначения.</span><span class="sxs-lookup"><span data-stu-id="113c6-619">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="113c6-620">Формирование URL-адресов для страниц поддерживает относительные имена.</span><span class="sxs-lookup"><span data-stu-id="113c6-620">URL generation for pages supports relative names.</span></span> <span data-ttu-id="113c6-621">В приведенной ниже таблице показано, какая страница индекса выбирается с каждым параметром `RedirectToPage` в *Pages/Customers/Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="113c6-621">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="113c6-622">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="113c6-622">RedirectToPage(x)</span></span>| <span data-ttu-id="113c6-623">Страница</span><span class="sxs-lookup"><span data-stu-id="113c6-623">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="113c6-624">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="113c6-624">RedirectToPage("/Index")</span></span> | <span data-ttu-id="113c6-625">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="113c6-625">*Pages/Index*</span></span> |
| <span data-ttu-id="113c6-626">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="113c6-626">RedirectToPage("./Index");</span></span> | <span data-ttu-id="113c6-627">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="113c6-627">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="113c6-628">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="113c6-628">RedirectToPage("../Index")</span></span> | <span data-ttu-id="113c6-629">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="113c6-629">*Pages/Index*</span></span> |
| <span data-ttu-id="113c6-630">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="113c6-630">RedirectToPage("Index")</span></span>  | <span data-ttu-id="113c6-631">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="113c6-631">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="113c6-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")` и `RedirectToPage("../Index")` — это *относительные имена*.</span><span class="sxs-lookup"><span data-stu-id="113c6-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="113c6-633">Для получения имени целевой страницы параметр `RedirectToPage`*комбинируется* с путем текущей страницы.</span><span class="sxs-lookup"><span data-stu-id="113c6-633">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="113c6-634">Привязка относительных имен полезна при создании сайтов со сложной структурой.</span><span class="sxs-lookup"><span data-stu-id="113c6-634">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="113c6-635">Если для связи между страницами в определенной папке используются относительные имена, эту папку можно переименовать.</span><span class="sxs-lookup"><span data-stu-id="113c6-635">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="113c6-636">При этом все ссылки останутся рабочими (так как не включают имя папки).</span><span class="sxs-lookup"><span data-stu-id="113c6-636">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="113c6-637">Чтобы выполнить перенаправление на страницу в другой [области](xref:mvc/controllers/areas), укажите эту область:</span><span class="sxs-lookup"><span data-stu-id="113c6-637">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="113c6-638">Для получения дополнительной информации см. <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="113c6-638">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="113c6-639">Атрибут ViewData</span><span class="sxs-lookup"><span data-stu-id="113c6-639">ViewData attribute</span></span>

<span data-ttu-id="113c6-640">Данные могут передаваться на страницу с помощью атрибута [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="113c6-640">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="113c6-641">Свойства в контроллерах или моделях Razor Page, отмеченные атрибутом `[ViewData]`, обладают собственными значениями, загружаемыми из [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="113c6-641">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="113c6-642">В следующем примере класс `AboutModel` содержит свойство `Title`, отмеченное атрибутом `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="113c6-642">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="113c6-643">Свойство `Title` задает заголовок страницы About.</span><span class="sxs-lookup"><span data-stu-id="113c6-643">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="113c6-644">На странице About доступ к свойству `Title` осуществляется как доступ к свойству модели.</span><span class="sxs-lookup"><span data-stu-id="113c6-644">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="113c6-645">В макете заголовок считывается из словаря ViewData.</span><span class="sxs-lookup"><span data-stu-id="113c6-645">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="113c6-646">TempData</span><span class="sxs-lookup"><span data-stu-id="113c6-646">TempData</span></span>

<span data-ttu-id="113c6-647">ASP.NET Core позволяет использовать свойство [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) в [контроллере](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="113c6-647">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="113c6-648">Это свойство хранит данные до тех пор, пока они не будут прочитаны.</span><span class="sxs-lookup"><span data-stu-id="113c6-648">This property stores data until it's read.</span></span> <span data-ttu-id="113c6-649">Для проверки данных без удаления можно использовать методы `Keep` и `Peek`.</span><span class="sxs-lookup"><span data-stu-id="113c6-649">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="113c6-650">`TempData` удобно использовать для перенаправления, когда данные требуются больше чем для одного запроса.</span><span class="sxs-lookup"><span data-stu-id="113c6-650">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="113c6-651">В следующем коде значение `Message` задается с помощью `TempData`:</span><span class="sxs-lookup"><span data-stu-id="113c6-651">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="113c6-652">Представленная ниже разметка в файле *Pages/Customers/Index.cshtml* отображает значение `Message` с помощью `TempData`.</span><span class="sxs-lookup"><span data-stu-id="113c6-652">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="113c6-653">Модель страницы *Pages/Customers/Index.cshtml.cs* применяет атрибут `[TempData]` к свойству `Message`.</span><span class="sxs-lookup"><span data-stu-id="113c6-653">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="113c6-654">Дополнительные сведения см. в разделе [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="113c6-654">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="113c6-655">Несколько обработчиков на страницу</span><span class="sxs-lookup"><span data-stu-id="113c6-655">Multiple handlers per page</span></span>

<span data-ttu-id="113c6-656">Следующая страница формирует разметку для двух обработчиков с помощью вспомогательной функции тегов `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="113c6-656">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="113c6-657">Форма в предыдущем примере включает две кнопки отправки, каждая из которых отправляет данные на отдельный URL-адрес с помощью `FormActionTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="113c6-657">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="113c6-658">Атрибут `asp-page-handler` является дополнением к `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="113c6-658">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="113c6-659">Атрибут `asp-page-handler` формирует URL-адреса, ,которые используются для отправки данных в каждый из методов обработчиков, определенных страницей.</span><span class="sxs-lookup"><span data-stu-id="113c6-659">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="113c6-660">`asp-page` не задается, так как пример сопоставлен с текущей страницей.</span><span class="sxs-lookup"><span data-stu-id="113c6-660">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="113c6-661">Модель страницы</span><span class="sxs-lookup"><span data-stu-id="113c6-661">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="113c6-662">В представленном выше коде используются *именованные методы обработчика*.</span><span class="sxs-lookup"><span data-stu-id="113c6-662">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="113c6-663">Именованные методы обработчика создаются путем размещения определенного текста в имени после `On<HTTP Verb>` и перед `Async` (если есть).</span><span class="sxs-lookup"><span data-stu-id="113c6-663">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="113c6-664">В приведенном выше примере использовались методы страницы OnPost**JoinList**Async и OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="113c6-664">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="113c6-665">Если убрать *OnPost* и *Async*, имена обработчиков будут выглядеть как `JoinList` и `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="113c6-665">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="113c6-666">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="113c6-666">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="113c6-667">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="113c6-667">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="113c6-668">Пользовательские маршруты</span><span class="sxs-lookup"><span data-stu-id="113c6-668">Custom routes</span></span>

<span data-ttu-id="113c6-669">С помощью директивы `@page` можно сделать следующее.</span><span class="sxs-lookup"><span data-stu-id="113c6-669">Use the `@page` directive to:</span></span>

* <span data-ttu-id="113c6-670">Указать пользовательский маршрут к странице.</span><span class="sxs-lookup"><span data-stu-id="113c6-670">Specify a custom route to a page.</span></span> <span data-ttu-id="113c6-671">Например, можно задать маршрут к странице "Сведения" `/Some/Other/Path`: `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="113c6-671">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="113c6-672">Добавить сегменты к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="113c6-672">Append segments to a page's default route.</span></span> <span data-ttu-id="113c6-673">Например, к такому маршруту можно добавить сегмент item: `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="113c6-673">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="113c6-674">Добавить параметры к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="113c6-674">Append parameters to a page's default route.</span></span> <span data-ttu-id="113c6-675">Например, для страницы с `@page "{id}"` может потребоваться параметр идентификатора `id`.</span><span class="sxs-lookup"><span data-stu-id="113c6-675">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="113c6-676">Поддерживается путь относительно корня, заданный знаком тильды (`~`) в начале пути.</span><span class="sxs-lookup"><span data-stu-id="113c6-676">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="113c6-677">Например, `@page "~/Some/Other/Path"` равносильно `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="113c6-677">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="113c6-678">Если вы не хотите, чтобы в URL-адресе отображалась строка запроса `?handler=JoinList`, измените маршрут так, чтобы в качестве пути в URL-адресе указывалось имя обработчика.</span><span class="sxs-lookup"><span data-stu-id="113c6-678">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="113c6-679">Для настройки маршрута добавьте после директивы `@page` шаблон маршрута, заключенный в двойные кавычки.</span><span class="sxs-lookup"><span data-stu-id="113c6-679">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="113c6-680">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="113c6-680">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="113c6-681">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="113c6-681">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="113c6-682">Символ `?` после `handler` означает, что параметр маршрута является необязательным.</span><span class="sxs-lookup"><span data-stu-id="113c6-682">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="113c6-683">Конфигурация и параметры</span><span class="sxs-lookup"><span data-stu-id="113c6-683">Configuration and settings</span></span>

<span data-ttu-id="113c6-684">Чтобы настроить дополнительные параметры, воспользуйтесь методом расширения `AddRazorPagesOptions` в построителе MVC:</span><span class="sxs-lookup"><span data-stu-id="113c6-684">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="113c6-685">В настоящее время `RazorPagesOptions` позволяет задавать корневую папку для страниц и добавлять для них обозначения моделей приложений.</span><span class="sxs-lookup"><span data-stu-id="113c6-685">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="113c6-686">В дальнейшем мы расширим спектр его возможностей.</span><span class="sxs-lookup"><span data-stu-id="113c6-686">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="113c6-687">Сведения о предварительной компиляции представлений см. на странице [Компиляция представлений Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="113c6-687">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="113c6-688">[Загрузить или просмотреть пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="113c6-688">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="113c6-689">Дополнительные общие сведения см. на странице [Учебник. Начало работы с Razor Pages в ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="113c6-689">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="113c6-690">Указание местонахождения Razor Pages в корне каталога</span><span class="sxs-lookup"><span data-stu-id="113c6-690">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="113c6-691">По умолчанию Razor Pages находится в корне каталога */Pages*.</span><span class="sxs-lookup"><span data-stu-id="113c6-691">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="113c6-692">Добавьте [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) в [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_), чтобы указать, что Razor Pages находится в [корневой папке содержимого](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) приложения:</span><span class="sxs-lookup"><span data-stu-id="113c6-692">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="113c6-693">Указание местонахождения Razor Pages в пользовательском корневом каталоге</span><span class="sxs-lookup"><span data-stu-id="113c6-693">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="113c6-694">Добавьте [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) в [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_), чтобы указать, что Razor Pages находится в пользовательском корневом каталоге в приложении (укажите относительный путь):</span><span class="sxs-lookup"><span data-stu-id="113c6-694">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="113c6-695">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="113c6-695">Additional resources</span></span>

* <span data-ttu-id="113c6-696">[Атрибут authorize и Razor Pages](xref:security/authorization/simple#aarp)</span><span class="sxs-lookup"><span data-stu-id="113c6-696">[Authorize attribute and Razor Pages](xref:security/authorization/simple#aarp)</span></span>
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
