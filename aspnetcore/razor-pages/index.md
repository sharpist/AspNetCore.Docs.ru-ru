---
title: 'Введение в Razor Pages в ASP.NET Core'
author: Rick-Anderson
description: 'Сведения о применении в ASP.NET Core функции Razor Pages, которая делает создание кодов сценариев для страниц проще и эффективнее по сравнению с MVC.'
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: razor-pages/index
ms.openlocfilehash: 89e06d00e9312a428c4e164b0dc60527fe12d904
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430878"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="46830-103">Введение в Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="46830-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="46830-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Райан Новак](https://github.com/rynowak) (Ryan Nowak)</span><span class="sxs-lookup"><span data-stu-id="46830-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="46830-105">Razor Pages делает создание кодов сценариев для страниц проще и эффективнее по сравнению с использованием контроллеров и представлений.</span><span class="sxs-lookup"><span data-stu-id="46830-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="46830-106">Если вам нужно руководство, использующее подход "модель-представление-контроллер", см. статью [Начало работы с MVC в ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="46830-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="46830-107">Этот документ содержит вводные сведения о Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="46830-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="46830-108">Это не пошаговое руководство.</span><span class="sxs-lookup"><span data-stu-id="46830-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="46830-109">Если некоторые разделы покажутся вам слишком сложными, см. [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="46830-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="46830-110">Общие сведения об ASP.NET Core см. в разделе [Введение в ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="46830-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="46830-111">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="46830-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46830-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46830-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="46830-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46830-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46830-114">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="46830-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="46830-115">Создание проекта Razor Pages</span><span class="sxs-lookup"><span data-stu-id="46830-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46830-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46830-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46830-117">Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="46830-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="46830-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46830-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="46830-119">Из командной строки выполните команду `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="46830-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46830-120">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="46830-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="46830-121">Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="46830-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="46830-122">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="46830-122">Razor Pages</span></span>

<span data-ttu-id="46830-123">Razor Pages активируется в файле *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="46830-123">Razor Pages is enabled in *Startup.cs* :</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="46830-124">Рассмотрим простую страницу: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="46830-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="46830-125">Приведенный выше код выглядит как [Razor файл представления](xref:tutorials/first-mvc-app/adding-view), используемый в приложениях ASP.NET Core с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="46830-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="46830-126">Он отличается от него только директивой [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="46830-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="46830-127">Директива `@page` превращает файл в действие MVC, а значит обрабатывает запросы напрямую, минуя контроллер.</span><span class="sxs-lookup"><span data-stu-id="46830-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="46830-128">`@page` должна быть первой директивой Razor на странице.</span><span class="sxs-lookup"><span data-stu-id="46830-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="46830-129">`@page` влияет на поведение всех остальных конструкций [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="46830-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="46830-130">Имена файлов Razor Pages имеют суффикс *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="46830-131">Похожая страница с использованием класса `PageModel` показана в следующих двух файлах.</span><span class="sxs-lookup"><span data-stu-id="46830-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="46830-132">Файл *Pages/Index2.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="46830-133">Модель страницы *Pages/Index2.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="46830-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="46830-134">Как правило, файл класса `PageModel` называется так же, как файл Razor Pages, но с расширением *CS*.</span><span class="sxs-lookup"><span data-stu-id="46830-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="46830-135">Например, представленная выше страница Razor Pages называется *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="46830-136">Файл, содержащий класс `PageModel`, называется *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="46830-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="46830-137">Сопоставления URL-адресов со страницами определяются расположением конкретной страницы в файловой системе.</span><span class="sxs-lookup"><span data-stu-id="46830-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="46830-138">В приведенной ниже таблице показаны пути Razor Pages и соответствующие URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="46830-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="46830-139">Имя файла и путь</span><span class="sxs-lookup"><span data-stu-id="46830-139">File name and path</span></span>               | <span data-ttu-id="46830-140">Соответствующий URL</span><span class="sxs-lookup"><span data-stu-id="46830-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="46830-141">*/Pages/index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="46830-142">`/` или `/Index`</span><span class="sxs-lookup"><span data-stu-id="46830-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="46830-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="46830-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="46830-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="46830-146">`/Store` или `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="46830-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="46830-147">Примечания.</span><span class="sxs-lookup"><span data-stu-id="46830-147">Notes:</span></span>

* <span data-ttu-id="46830-148">Среда выполнения по умолчанию ищет файлы Razor Pages в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="46830-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="46830-149">Если в URL-адресе не указана конкретная страница, по умолчанию открывается страница `Index`.</span><span class="sxs-lookup"><span data-stu-id="46830-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="46830-150">Создание простой формы</span><span class="sxs-lookup"><span data-stu-id="46830-150">Write a basic form</span></span>

<span data-ttu-id="46830-151">Razor Pages предназначена для упрощения реализации типовых шаблонов, которые используются в браузерах, при создании приложения.</span><span class="sxs-lookup"><span data-stu-id="46830-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="46830-152">[Привязки модели](xref:mvc/models/model-binding), [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и вспомогательные методы HTML *отлично работают* со свойствами, определенными в классе Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="46830-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="46830-153">Рассмотрим страницу с простой формой связи для модели `Contact`.</span><span class="sxs-lookup"><span data-stu-id="46830-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="46830-154">В представленных в этой статье примерах `DbContext` инициализируется в файле [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="46830-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="46830-155">Для работы с базой данных в памяти требуется пакет NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="46830-155">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="46830-156">Модель данных:</span><span class="sxs-lookup"><span data-stu-id="46830-156">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="46830-157">Контекст базы данных:</span><span class="sxs-lookup"><span data-stu-id="46830-157">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="46830-158">Файл представления *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="46830-159">Модель страницы *Pages/Create.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="46830-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="46830-160">Как правило, класс `PageModel` называется `<PageName>Model` и находится в том же пространстве имен, что и страница.</span><span class="sxs-lookup"><span data-stu-id="46830-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="46830-161">Класс `PageModel` позволяет разделять логику страницы и ее представление.</span><span class="sxs-lookup"><span data-stu-id="46830-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="46830-162">Он определяет обработчики страницы для запросов, отправляемых на страницу, а также данные для ее визуализации.</span><span class="sxs-lookup"><span data-stu-id="46830-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="46830-163">Это разделение позволяет:</span><span class="sxs-lookup"><span data-stu-id="46830-163">This separation allows:</span></span>

* <span data-ttu-id="46830-164">управлять зависимостями страниц с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="46830-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="46830-165">Модульное тестирование</span><span class="sxs-lookup"><span data-stu-id="46830-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="46830-166">Страница содержит *метод обработчика* `OnPostAsync`, который выполняется по запросам `POST` (когда пользователь публикует форму).</span><span class="sxs-lookup"><span data-stu-id="46830-166">The page has an `OnPostAsync` *handler method* , which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="46830-167">Можно добавить методы обработчика для любой HTTP-команды.</span><span class="sxs-lookup"><span data-stu-id="46830-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="46830-168">Наиболее распространенные обработчики</span><span class="sxs-lookup"><span data-stu-id="46830-168">The most common handlers are:</span></span>

* <span data-ttu-id="46830-169">`OnGet` — инициализация необходимого для страницы состояния.</span><span class="sxs-lookup"><span data-stu-id="46830-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="46830-170">В приведенном выше коде метод `OnGet` отображает страницу Razor *CreateModel.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="46830-171">`OnPost` — обработка отправленных через форму данных.</span><span class="sxs-lookup"><span data-stu-id="46830-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="46830-172">Суффикс `Async` не является обязательным, но часто используется для асинхронных функций.</span><span class="sxs-lookup"><span data-stu-id="46830-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="46830-173">Этот код типичен для Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="46830-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="46830-174">Если вы знакомы с приложениями ASP.NET, использующими контроллеры и представления:</span><span class="sxs-lookup"><span data-stu-id="46830-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="46830-175">Код `OnPostAsync` в предыдущем примере похож на стандартный код контроллера.</span><span class="sxs-lookup"><span data-stu-id="46830-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="46830-176">Большинство примитивов MVC, включая [привязку модели](xref:mvc/models/model-binding), [проверку](xref:mvc/models/validation) и результаты действий, одинаково работают с контроллерами и Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="46830-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="46830-177">Предыдущий метод `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="46830-177">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="46830-178">Простая схема `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="46830-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="46830-179">Проверка на наличие ошибок проверки.</span><span class="sxs-lookup"><span data-stu-id="46830-179">Check for validation errors.</span></span>

* <span data-ttu-id="46830-180">Если ошибок нет, сохранение данных и перенаправление.</span><span class="sxs-lookup"><span data-stu-id="46830-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="46830-181">Если есть ошибки, отображение страницы с сообщениями проверки.</span><span class="sxs-lookup"><span data-stu-id="46830-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="46830-182">Во многих случаях ошибки проверки выявляются на клиентском компьютере и на сервер не отправляются.</span><span class="sxs-lookup"><span data-stu-id="46830-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="46830-183">Файл представления *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="46830-184">Преобразованный HTML из *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-184">The rendered HTML from *Pages/Create.cshtml* :</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="46830-185">В приведенном выше коде, разместив форму:</span><span class="sxs-lookup"><span data-stu-id="46830-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="46830-186">С допустимыми данными:</span><span class="sxs-lookup"><span data-stu-id="46830-186">With valid data:</span></span>

  * <span data-ttu-id="46830-187">Метод обработчика `OnPostAsync` вызывает вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>.</span><span class="sxs-lookup"><span data-stu-id="46830-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="46830-188">`RedirectToPage` возвращает экземпляр <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="46830-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="46830-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="46830-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="46830-190">Является результатом действия.</span><span class="sxs-lookup"><span data-stu-id="46830-190">Is an action result.</span></span>
    * <span data-ttu-id="46830-191">Аналогичен `RedirectToAction` или `RedirectToRoute` (используется в контроллерах и представлениях).</span><span class="sxs-lookup"><span data-stu-id="46830-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="46830-192">Настраивается для страниц.</span><span class="sxs-lookup"><span data-stu-id="46830-192">Is customized for pages.</span></span> <span data-ttu-id="46830-193">В приведенном выше примере он выполняет перенаправление на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="46830-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="46830-194">Более подробно `RedirectToPage` рассматривается в разделе [Создание URL для страниц](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="46830-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="46830-195">С ошибками проверки, передаваемыми на сервер:</span><span class="sxs-lookup"><span data-stu-id="46830-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="46830-196">Метод обработчика `OnPostAsync` вызывает вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>.</span><span class="sxs-lookup"><span data-stu-id="46830-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="46830-197">`Page` возвращает экземпляр <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="46830-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="46830-198">Возвращение `Page` аналогично тому, как действия в контроллерах возвращают `View`.</span><span class="sxs-lookup"><span data-stu-id="46830-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="46830-199">`PageResult` — тип возвращаемого значения по умолчанию для метода обработчика.</span><span class="sxs-lookup"><span data-stu-id="46830-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="46830-200">Метод обработчика, вернувший `void`, визуализирует страницу.</span><span class="sxs-lookup"><span data-stu-id="46830-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="46830-201">В предыдущем примере публикация формы без значения приводит к тому, что [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) возвращает значение false.</span><span class="sxs-lookup"><span data-stu-id="46830-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="46830-202">В этом примере на клиенте не отображаются ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="46830-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="46830-203">Обработка ошибок проверки рассматривается далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="46830-203">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="46830-204">С ошибками проверки, обнаруженными при проверке на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="46830-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="46830-205">Данные **не** отправляются на сервер.</span><span class="sxs-lookup"><span data-stu-id="46830-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="46830-206">Проверка на стороне клиента описана далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="46830-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="46830-207">Для указания согласия на привязку модели в свойстве `Customer` используется атрибут [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute):</span><span class="sxs-lookup"><span data-stu-id="46830-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="46830-208">`[BindProperty]`**не** следует использовать в моделях, содержащих свойства, которые не должны изменяться клиентом.</span><span class="sxs-lookup"><span data-stu-id="46830-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="46830-209">Дополнительные сведения см. в [этом разделе](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="46830-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="46830-210">По умолчанию Razor привязывает свойства ко всем командам, кроме `GET`.</span><span class="sxs-lookup"><span data-stu-id="46830-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="46830-211">Привязка к свойствам устраняет необходимость в написании кода для преобразования данных HTTP в тип модели.</span><span class="sxs-lookup"><span data-stu-id="46830-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="46830-212">Привязка уменьшает код за счет того, что для визуализации полей формы (`<input asp-for="Customer.Name">`) и получения входных данных используется одно и то же свойство.</span><span class="sxs-lookup"><span data-stu-id="46830-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="46830-213">Просмотр файла представления *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="46830-214">В приведенном выше коде [вспомогательная функция тега ввода](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` привязывает элемент `<input>` HTML к выражению модели `Customer.Name`.</span><span class="sxs-lookup"><span data-stu-id="46830-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="46830-215">Директива [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) обеспечивает доступность вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="46830-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="46830-216">Домашняя страница</span><span class="sxs-lookup"><span data-stu-id="46830-216">The home page</span></span>

<span data-ttu-id="46830-217">*Index.cshtml*  — это домашняя страница:</span><span class="sxs-lookup"><span data-stu-id="46830-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="46830-218">Связанный класс `PageModel` ( *Index.cshtml.cs* ):</span><span class="sxs-lookup"><span data-stu-id="46830-218">The associated `PageModel` class ( *Index.cshtml.cs* ):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="46830-219">Файл *index.cshtml* содержит следующую разметку:</span><span class="sxs-lookup"><span data-stu-id="46830-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="46830-220">[Вспомогательный тег привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a /a>` использовал атрибут `asp-route-{value}` для создания ссылки на страницу редактирования.</span><span class="sxs-lookup"><span data-stu-id="46830-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="46830-221">Эта ссылка содержит данные о маршруте с идентификатором контактного лица.</span><span class="sxs-lookup"><span data-stu-id="46830-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="46830-222">Например, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="46830-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="46830-223">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="46830-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="46830-224">Файл *Index.cshtml* содержит разметку для создания кнопки удаления у каждого контакта клиента:</span><span class="sxs-lookup"><span data-stu-id="46830-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="46830-225">Отображаемый HTML:</span><span class="sxs-lookup"><span data-stu-id="46830-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="46830-226">Во время обработки кнопки удаления в HTML ее [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) включает параметры для следующего:</span><span class="sxs-lookup"><span data-stu-id="46830-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="46830-227">Идентификатор контакта клиента, указанный атрибутом `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="46830-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="46830-228">Параметр `handler`, указанный атрибутом `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="46830-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="46830-229">При выборе кнопки на сервер отправляется запрос формы `POST`.</span><span class="sxs-lookup"><span data-stu-id="46830-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="46830-230">По соглашению имя метода обработчика выбирается на основе значения параметра `handler` в соответствии со схемой `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="46830-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="46830-231">Так как `handler` — `delete` в этом примере, метод обработчика `OnPostDeleteAsync` используется для обработки запроса `POST`.</span><span class="sxs-lookup"><span data-stu-id="46830-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="46830-232">Если `asp-page-handler` имеет другое значение, например `remove`, выбирается метод обработчика с именем `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="46830-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="46830-233">Метод `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="46830-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="46830-234">Получает `id` из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="46830-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="46830-235">Отправляет в базу данных запрос контакта клиента с `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="46830-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="46830-236">Если контакт клиента найден, он удаляется, и база данных обновляется.</span><span class="sxs-lookup"><span data-stu-id="46830-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="46830-237">Вызывает <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> для перенаправления на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="46830-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="46830-238">Файл Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="46830-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="46830-239">Первая строка содержит директиву `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="46830-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="46830-240">Ограничение маршрутизации `"{id:int}"` указывает, что страница должна принимать обращенные к ней запросы, которые содержат данные о маршруте `int`.</span><span class="sxs-lookup"><span data-stu-id="46830-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="46830-241">Если запрос к странице не содержит данные о маршруте, которые можно конвертировать в `int`, среда выполнения возвращает ошибку HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="46830-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="46830-242">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="46830-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="46830-243">Файл *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="46830-243">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="46830-244">Проверка</span><span class="sxs-lookup"><span data-stu-id="46830-244">Validation</span></span>

<span data-ttu-id="46830-245">Правила проверки:</span><span class="sxs-lookup"><span data-stu-id="46830-245">Validation rules:</span></span>

* <span data-ttu-id="46830-246">Декларативно задаются в классе Model.</span><span class="sxs-lookup"><span data-stu-id="46830-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="46830-247">Применяются везде в приложении.</span><span class="sxs-lookup"><span data-stu-id="46830-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="46830-248">Пространство имен <xref:System.ComponentModel.DataAnnotations> предоставляет набор встроенных атрибутов проверки, которые декларативно применяются к классу или свойству.</span><span class="sxs-lookup"><span data-stu-id="46830-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="46830-249">Кроме того, DataAnnotations содержит атрибуты форматирования (такие как [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)), которые обеспечивают форматирование и не предназначены для проверки.</span><span class="sxs-lookup"><span data-stu-id="46830-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="46830-250">Рассмотрим модель `Customer`:</span><span class="sxs-lookup"><span data-stu-id="46830-250">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="46830-251">Используя следующий файл представления *Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="46830-252">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="46830-252">The preceding code:</span></span>

* <span data-ttu-id="46830-253">Включает jQuery и скрипты проверки jQuery.</span><span class="sxs-lookup"><span data-stu-id="46830-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="46830-254">Использует [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) `<div />` и `<span />` для следующих задач:</span><span class="sxs-lookup"><span data-stu-id="46830-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="46830-255">Проверка на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="46830-255">Client-side validation.</span></span>
  * <span data-ttu-id="46830-256">Отображение ошибок при проверке.</span><span class="sxs-lookup"><span data-stu-id="46830-256">Validation error rendering.</span></span>

* <span data-ttu-id="46830-257">Генерирует следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="46830-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="46830-258">При публикации формы создания без значения имени в форме отображается сообщение об ошибке: "Поле имени является обязательным".</span><span class="sxs-lookup"><span data-stu-id="46830-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="46830-259">в форме.</span><span class="sxs-lookup"><span data-stu-id="46830-259">on the form.</span></span> <span data-ttu-id="46830-260">Если на клиенте включен JavaScript, браузер отображает ошибку без отправки на сервер.</span><span class="sxs-lookup"><span data-stu-id="46830-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="46830-261">Атрибут `[StringLength(10)]` создает `data-val-length-max="10"` в отображаемом HTML-коде.</span><span class="sxs-lookup"><span data-stu-id="46830-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="46830-262">`data-val-length-max` не дает браузерам ввести больше заданной максимальной длины.</span><span class="sxs-lookup"><span data-stu-id="46830-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="46830-263">Если для изменения и воспроизведения записи используется средство, например [Fiddler](https://www.telerik.com/fiddler), выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="46830-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="46830-264">С именем, превышающим 10.</span><span class="sxs-lookup"><span data-stu-id="46830-264">With the name longer than 10.</span></span>
* <span data-ttu-id="46830-265">Возвращается сообщение об ошибке: "Имя поля должно быть строкой с максимальной длиной 10".</span><span class="sxs-lookup"><span data-stu-id="46830-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="46830-266">.</span><span class="sxs-lookup"><span data-stu-id="46830-266">is returned.</span></span>

<span data-ttu-id="46830-267">Рассмотрим следующую модель `Movie`:</span><span class="sxs-lookup"><span data-stu-id="46830-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="46830-268">Атрибуты проверки определяют поведение для свойств модели, к которым они применяются:</span><span class="sxs-lookup"><span data-stu-id="46830-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="46830-269">Атрибуты `Required` и `MinimumLength` указывают, что свойство должно иметь значение. Тем не менее, чтобы удовлетворить требованиям проверки, пользователю достаточно ввести пробел.</span><span class="sxs-lookup"><span data-stu-id="46830-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="46830-270">Атрибут `RegularExpression` ограничивает набор допустимых для ввода символов.</span><span class="sxs-lookup"><span data-stu-id="46830-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="46830-271">В приведенном выше коде в Genre:</span><span class="sxs-lookup"><span data-stu-id="46830-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="46830-272">должны использоваться только буквы;</span><span class="sxs-lookup"><span data-stu-id="46830-272">Must only use letters.</span></span>
  * <span data-ttu-id="46830-273">первая буква должна быть прописной;</span><span class="sxs-lookup"><span data-stu-id="46830-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="46830-274">пробелы, цифры и специальные символы не допускаются.</span><span class="sxs-lookup"><span data-stu-id="46830-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="46830-275">В `RegularExpression` Rating:</span><span class="sxs-lookup"><span data-stu-id="46830-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="46830-276">первый символ должен быть прописной буквой;</span><span class="sxs-lookup"><span data-stu-id="46830-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="46830-277">допускаются специальные символы и цифры, а также последующие пробелы.</span><span class="sxs-lookup"><span data-stu-id="46830-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="46830-278">Значение "PG-13" допустимо для рейтинга, но недопустимо для жанра.</span><span class="sxs-lookup"><span data-stu-id="46830-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="46830-279">Атрибут `Range` ограничивает диапазон значений.</span><span class="sxs-lookup"><span data-stu-id="46830-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="46830-280">Атрибут `StringLength` задает максимальную и при необходимости минимальную длину строкового свойства.</span><span class="sxs-lookup"><span data-stu-id="46830-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="46830-281">Типы значений (например, `decimal`, `int`, `float`, `DateTime`) по своей природе являются обязательными и не требуют атрибута `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="46830-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="46830-282">На странице создания для модели `Movie` отображаются ошибки с недопустимыми значениями:</span><span class="sxs-lookup"><span data-stu-id="46830-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Форма просмотра фильма с несколькими ошибками проверки jQuery на стороне клиента](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="46830-284">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="46830-284">For more information, see:</span></span>

* [<span data-ttu-id="46830-285">Добавление проверки в приложение Movie</span><span class="sxs-lookup"><span data-stu-id="46830-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="46830-286">[Проверка модели в ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="46830-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="46830-287">Обработка запросов HEAD с помощью вызова резервного обработчика OnGet</span><span class="sxs-lookup"><span data-stu-id="46830-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="46830-288">Запросы `HEAD` позволяют получать заголовки для определенного ресурса.</span><span class="sxs-lookup"><span data-stu-id="46830-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="46830-289">В отличие от запросов `GET` запросы `HEAD`не возвращают текст ответа.</span><span class="sxs-lookup"><span data-stu-id="46830-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="46830-290">Обработчик `OnHead` обычно создается и вызывается для выполнения запросов `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="46830-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="46830-291">Если обработчик `OnHead` не определен, Razor Pages выполнит вызов обработчика `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="46830-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="46830-292">XSRF/CSRF и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="46830-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="46830-293">В Razor Pages реализована [проверка для защиты от подделки](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="46830-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="46830-294">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) вставляет маркеры защиты от подделки в элементы HTML-форм.</span><span class="sxs-lookup"><span data-stu-id="46830-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="46830-295">Использование макетов, частичных реплик, шаблонов и вспомогательных функций тегов с Razor Pages</span><span class="sxs-lookup"><span data-stu-id="46830-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="46830-296">Pages работает со всеми функциями подсистемы просмотра Razor.</span><span class="sxs-lookup"><span data-stu-id="46830-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="46830-297">Макеты, частичные реплики, шаблоны, вспомогательные функции тегов, а также файлы *_ViewStart.cshtml* и *_ViewImports.cshtml* работают точно так же, как и в стандартных представлениях Razor.</span><span class="sxs-lookup"><span data-stu-id="46830-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml* , and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="46830-298">Давайте упростим нашу страницу с помощью некоторых из этих функций.</span><span class="sxs-lookup"><span data-stu-id="46830-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="46830-299">Добавим [макет страницы](xref:mvc/views/layout) в файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml* :</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="46830-300">Этот [макет](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="46830-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="46830-301">управляет макетом каждой страницы (кроме страниц с отказом от макета);</span><span class="sxs-lookup"><span data-stu-id="46830-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="46830-302">импортирует HTML-структуры, такие как JavaScript и таблицы стилей.</span><span class="sxs-lookup"><span data-stu-id="46830-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="46830-303">Содержимое страницы Razor отображается в том месте, где вызывается `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="46830-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="46830-304">Дополнительные сведения см. [здесь](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="46830-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="46830-305">Свойство [Layout](xref:mvc/views/layout#specifying-a-layout) определяется в файле *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml* :</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="46830-306">Макет хранится в папке *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="46830-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="46830-307">Pages ищет другие представления (макеты, шаблоны, частичные реплики) в иерархическом порядке, начиная с той папки, где находится текущая страница.</span><span class="sxs-lookup"><span data-stu-id="46830-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="46830-308">Макет в папке *Pages/Shared* можно использовать на любой странице Razor, которая находится в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="46830-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="46830-309">Файл макета следует поместить в папку *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="46830-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="46830-310">Корпорация Майкрософт рекомендует **не** размещать файл макета в папке *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="46830-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="46830-311">*Views/Shared*  — это шаблон представлений MVC.</span><span class="sxs-lookup"><span data-stu-id="46830-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="46830-312">Razor Pages опирается на иерархию папок, а не на условные обозначения путей.</span><span class="sxs-lookup"><span data-stu-id="46830-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="46830-313">Поиск представлений в Razor Pages охватывает папку *Pages*.</span><span class="sxs-lookup"><span data-stu-id="46830-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="46830-314">Макеты, шаблоны и частичные реплики *работают* с контроллерами MVC и стандартными представлениями Razor.</span><span class="sxs-lookup"><span data-stu-id="46830-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="46830-315">Добавим файл *Pages/_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="46830-316">`@namespace` описывается далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="46830-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="46830-317">Директива `@addTagHelper` добавляет [встроенные вспомогательные теги](xref:mvc/views/tag-helpers/builtin-th/Index) на все страницы в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="46830-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="46830-318">Директива `@namespace`, заданная на странице:</span><span class="sxs-lookup"><span data-stu-id="46830-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="46830-319">Директива `@namespace` задает пространство имен для страницы.</span><span class="sxs-lookup"><span data-stu-id="46830-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="46830-320">Включать пространство имен в директиву `@model` не требуется.</span><span class="sxs-lookup"><span data-stu-id="46830-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="46830-321">Если директива `@namespace` содержится в файле *_ViewImports.cshtml* , указанное пространство имен определяет префикс для созданного в Pages пространства имен, куда импортируется директива `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="46830-321">When the `@namespace` directive is contained in *_ViewImports.cshtml* , the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="46830-322">Остальная часть созданного пространства имен (суффикс) представляет собой разделенный точками относительный путь между папкой с файлом *_ViewImports.cshtml* и папкой, содержащей страницу.</span><span class="sxs-lookup"><span data-stu-id="46830-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="46830-323">Например, класс `PageModel` в файле *Pages/Customers/Edit.cshtml.cs* задает пространство имен явно.</span><span class="sxs-lookup"><span data-stu-id="46830-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="46830-324">Файл *Pages/_ViewImports.cshtml* задает следующее пространство имен:</span><span class="sxs-lookup"><span data-stu-id="46830-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="46830-325">Сформированное пространство имен для файла *Pages/Customers/Edit.cshtml* Razor Pages совпадает с пространством имен класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="46830-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="46830-326">`@namespace` *также работает со стандартными представлениями Razor.*</span><span class="sxs-lookup"><span data-stu-id="46830-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="46830-327">Рассмотрим файл представления *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="46830-328">Обновленный файл представления *Pages/Create.cshtml* с *_ViewImports.cshtml* и предыдущим файлом макета:</span><span class="sxs-lookup"><span data-stu-id="46830-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="46830-329">В приведенном выше коде *_ViewImports. cshtml* импортировал пространство имен и вспомогательные функции тегов.</span><span class="sxs-lookup"><span data-stu-id="46830-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="46830-330">Файл макета импортировал файлы JavaScript.</span><span class="sxs-lookup"><span data-stu-id="46830-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="46830-331">[Начальный проект Razor Pages](#rpvs17) содержит файл *Pages/_ValidationScriptsPartial.cshtml* , который подключает проверку на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="46830-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml* , which hooks up client-side validation.</span></span>

<span data-ttu-id="46830-332">Дополнительные сведения о частичных представлениях см. в <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="46830-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="46830-333">Формирование URL-адресов для страниц</span><span class="sxs-lookup"><span data-stu-id="46830-333">URL generation for Pages</span></span>

<span data-ttu-id="46830-334">На представленной выше странице `Create` используется `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="46830-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="46830-335">Это приложение имеет следующую структуру файлов и папок:</span><span class="sxs-lookup"><span data-stu-id="46830-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="46830-336">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="46830-336">*/Pages*</span></span>

  * <span data-ttu-id="46830-337">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="46830-338">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="46830-339">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="46830-339">*/Customers*</span></span>

    * <span data-ttu-id="46830-340">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="46830-341">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="46830-342">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-342">*Index.cshtml*</span></span>

<span data-ttu-id="46830-343">После успешного выполнения страницы *Pages/Customers/Create.cshtml* и *Pages/Customers/Edit.cshtml* перенаправляются на страницу *Pages/Customers/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="46830-344">Строка `./Index` — это относительное имя страницы, используемое для доступа к предыдущей странице.</span><span class="sxs-lookup"><span data-stu-id="46830-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="46830-345">Эта строка позволяет создавать URL-адреса для страницы *Pages/Customers/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="46830-346">Пример:</span><span class="sxs-lookup"><span data-stu-id="46830-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="46830-347">С помощью абсолютного имени страницы `/Index` создаются URL-адреса страницы *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="46830-348">Пример:</span><span class="sxs-lookup"><span data-stu-id="46830-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="46830-349">Имя страницы — это путь к странице из корневой папки */Pages* , включая начальный символ `/` (например, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="46830-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="46830-350">Предыдущие образцы создания URL-адреса обеспечивают расширенные параметры и функциональные возможности по сравнению с жестким заданием URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="46830-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="46830-351">Формирование URL-адресов включает [маршрутизацию](xref:mvc/controllers/routing) и позволяет генерировать и включать в код параметры в зависимости от того, как определяется маршрут в пути назначения.</span><span class="sxs-lookup"><span data-stu-id="46830-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="46830-352">Формирование URL-адресов для страниц поддерживает относительные имена.</span><span class="sxs-lookup"><span data-stu-id="46830-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="46830-353">В приведенной ниже таблице показано, какая страница индекса выбирается с помощью разных параметров `RedirectToPage` в *Pages/Customers/Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="46830-354">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="46830-354">RedirectToPage(x)</span></span>| <span data-ttu-id="46830-355">Страница</span><span class="sxs-lookup"><span data-stu-id="46830-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="46830-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="46830-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="46830-357">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="46830-357">*Pages/Index*</span></span> |
| <span data-ttu-id="46830-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="46830-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="46830-359">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="46830-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="46830-360">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="46830-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="46830-361">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="46830-361">*Pages/Index*</span></span> |
| <span data-ttu-id="46830-362">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="46830-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="46830-363">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="46830-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="46830-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")` и `RedirectToPage("../Index")` — это *относительные имена*.</span><span class="sxs-lookup"><span data-stu-id="46830-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="46830-365">Для получения имени целевой страницы параметр `RedirectToPage`*комбинируется* с путем текущей страницы.</span><span class="sxs-lookup"><span data-stu-id="46830-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="46830-366">Привязка относительных имен полезна при создании сайтов со сложной структурой.</span><span class="sxs-lookup"><span data-stu-id="46830-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="46830-367">Если относительные имена используются для связи между страницами в папке:</span><span class="sxs-lookup"><span data-stu-id="46830-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="46830-368">Переименование папки не нарушает относительные ссылки.</span><span class="sxs-lookup"><span data-stu-id="46830-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="46830-369">Ссылки не нарушаются, так как не содержат имя папки.</span><span class="sxs-lookup"><span data-stu-id="46830-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="46830-370">Чтобы выполнить перенаправление на страницу в другой [области](xref:mvc/controllers/areas), укажите эту область:</span><span class="sxs-lookup"><span data-stu-id="46830-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="46830-371">Дополнительные сведения см. в разделах <xref:mvc/controllers/areas> и <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="46830-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="46830-372">Атрибут ViewData</span><span class="sxs-lookup"><span data-stu-id="46830-372">ViewData attribute</span></span>

<span data-ttu-id="46830-373">Данные могут передаваться на страницу с помощью атрибута <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span><span class="sxs-lookup"><span data-stu-id="46830-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="46830-374">Значения свойств с атрибутом `[ViewData]` хранятся в <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> и загружаются из него.</span><span class="sxs-lookup"><span data-stu-id="46830-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="46830-375">В следующем примере `AboutModel` применяет атрибут `[ViewData]` к свойству `Title`:</span><span class="sxs-lookup"><span data-stu-id="46830-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="46830-376">На странице About доступ к свойству `Title` осуществляется как доступ к свойству модели.</span><span class="sxs-lookup"><span data-stu-id="46830-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="46830-377">В макете заголовок считывается из словаря ViewData.</span><span class="sxs-lookup"><span data-stu-id="46830-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="46830-378">TempData</span><span class="sxs-lookup"><span data-stu-id="46830-378">TempData</span></span>

<span data-ttu-id="46830-379">ASP.NET Core предоставляет <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span><span class="sxs-lookup"><span data-stu-id="46830-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="46830-380">Это свойство хранит данные до тех пор, пока они не будут прочитаны.</span><span class="sxs-lookup"><span data-stu-id="46830-380">This property stores data until it's read.</span></span> <span data-ttu-id="46830-381">Для проверки данных без удаления можно использовать методы <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> и <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*>.</span><span class="sxs-lookup"><span data-stu-id="46830-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="46830-382">`TempData` удобно использовать для перенаправления, когда данные требуются больше, чем для одного запроса.</span><span class="sxs-lookup"><span data-stu-id="46830-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="46830-383">В следующем коде значение `Message` задается с помощью `TempData`:</span><span class="sxs-lookup"><span data-stu-id="46830-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="46830-384">Представленная ниже разметка в файле *Pages/Customers/Index.cshtml* отображает значение `Message` с помощью `TempData`.</span><span class="sxs-lookup"><span data-stu-id="46830-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="46830-385">Модель страницы *Pages/Customers/Index.cshtml.cs* применяет атрибут `[TempData]` к свойству `Message`.</span><span class="sxs-lookup"><span data-stu-id="46830-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="46830-386">Дополнительные сведения см. в разделе [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="46830-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="46830-387">Несколько обработчиков на страницу</span><span class="sxs-lookup"><span data-stu-id="46830-387">Multiple handlers per page</span></span>

<span data-ttu-id="46830-388">Следующая страница формирует разметку для двух обработчиков с помощью вспомогательной функции тегов `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="46830-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="46830-389">Форма в предыдущем примере включает две кнопки отправки, каждая из которых отправляет данные на отдельный URL-адрес с помощью `FormActionTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="46830-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="46830-390">Атрибут `asp-page-handler` является дополнением к `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="46830-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="46830-391">Атрибут `asp-page-handler` формирует URL-адреса, ,которые используются для отправки данных в каждый из методов обработчиков, определенных страницей.</span><span class="sxs-lookup"><span data-stu-id="46830-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="46830-392">`asp-page` не задается, так как пример сопоставлен с текущей страницей.</span><span class="sxs-lookup"><span data-stu-id="46830-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="46830-393">Модель страницы</span><span class="sxs-lookup"><span data-stu-id="46830-393">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="46830-394">В представленном выше коде используются *именованные методы обработчика*.</span><span class="sxs-lookup"><span data-stu-id="46830-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="46830-395">Именованные методы обработчика создаются путем размещения определенного текста в имени после `On<HTTP Verb>` и перед `Async` (если есть).</span><span class="sxs-lookup"><span data-stu-id="46830-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="46830-396">В приведенном выше примере использовались методы страницы OnPost **JoinList** Async и OnPost **JoinListUC** Async.</span><span class="sxs-lookup"><span data-stu-id="46830-396">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="46830-397">Если убрать *OnPost* и *Async* , имена обработчиков будут выглядеть как `JoinList` и `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="46830-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="46830-398">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="46830-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="46830-399">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="46830-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="46830-400">Пользовательские маршруты</span><span class="sxs-lookup"><span data-stu-id="46830-400">Custom routes</span></span>

<span data-ttu-id="46830-401">С помощью директивы `@page` можно сделать следующее.</span><span class="sxs-lookup"><span data-stu-id="46830-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="46830-402">Указать пользовательский маршрут к странице.</span><span class="sxs-lookup"><span data-stu-id="46830-402">Specify a custom route to a page.</span></span> <span data-ttu-id="46830-403">Например, можно задать маршрут к странице "Сведения" `/Some/Other/Path`: `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="46830-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="46830-404">Добавить сегменты к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="46830-404">Append segments to a page's default route.</span></span> <span data-ttu-id="46830-405">Например, к такому маршруту можно добавить сегмент item: `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="46830-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="46830-406">Добавить параметры к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="46830-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="46830-407">Например, для страницы с `@page "{id}"` может потребоваться параметр идентификатора `id`.</span><span class="sxs-lookup"><span data-stu-id="46830-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="46830-408">Поддерживается путь относительно корня, заданный знаком тильды (`~`) в начале пути.</span><span class="sxs-lookup"><span data-stu-id="46830-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="46830-409">Например, `@page "~/Some/Other/Path"` равносильно `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="46830-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="46830-410">Если вы не хотите, чтобы в URL-адресе отображалась строка запроса `?handler=JoinList`, измените маршрут так, чтобы в качестве пути в URL-адресе указывалось имя обработчика.</span><span class="sxs-lookup"><span data-stu-id="46830-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="46830-411">Для настройки маршрута добавьте после директивы `@page` шаблон маршрута, заключенный в двойные кавычки.</span><span class="sxs-lookup"><span data-stu-id="46830-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="46830-412">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="46830-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="46830-413">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="46830-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="46830-414">Символ `?` после `handler` означает, что параметр маршрута является необязательным.</span><span class="sxs-lookup"><span data-stu-id="46830-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="46830-415">Расширенная конфигурация и параметры</span><span class="sxs-lookup"><span data-stu-id="46830-415">Advanced configuration and settings</span></span>

<span data-ttu-id="46830-416">Конфигурация и параметры в следующих разделах не требуются большинству приложений.</span><span class="sxs-lookup"><span data-stu-id="46830-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="46830-417">Для настройки расширенных параметров используйте перегрузку <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>, которая настраивает <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span><span class="sxs-lookup"><span data-stu-id="46830-417">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="46830-418">Используйте <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>, чтобы задавать корневой каталог для страниц и добавлять для них соглашения моделей приложений.</span><span class="sxs-lookup"><span data-stu-id="46830-418">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="46830-419">Дополнительные сведения о соглашениях см. в разделе [Соглашения об авторизации Razor Pages](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="46830-419">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="46830-420">Сведения о предварительной компиляции представлений см. на странице [Компиляция представлений Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="46830-420">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="46830-421">Указание местонахождения Razor Pages в корне каталога</span><span class="sxs-lookup"><span data-stu-id="46830-421">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="46830-422">По умолчанию Razor Pages находится в корне каталога */Pages*.</span><span class="sxs-lookup"><span data-stu-id="46830-422">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="46830-423">Добавьте <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*>, чтобы указать, что Razor Pages находится в [корневой папке содержимого](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) приложения:</span><span class="sxs-lookup"><span data-stu-id="46830-423">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="46830-424">Указание местонахождения Razor Pages в пользовательском корневом каталоге</span><span class="sxs-lookup"><span data-stu-id="46830-424">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="46830-425">Добавьте <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*>, чтобы указать, что Razor Pages находится в пользовательском корневом каталоге в приложении (укажите относительный путь):</span><span class="sxs-lookup"><span data-stu-id="46830-425">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="46830-426">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="46830-426">Additional resources</span></span>

* <span data-ttu-id="46830-427">Дополнительные общие сведения см. на странице [Учебник. Начало работы с Razor Pages в ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="46830-427">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="46830-428">Атрибут authorize и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="46830-428">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <span data-ttu-id="46830-429">[Загрузить или просмотреть пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample).</span><span class="sxs-lookup"><span data-stu-id="46830-429">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)</span></span>
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="46830-430">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Райан Новак](https://github.com/rynowak) (Ryan Nowak)</span><span class="sxs-lookup"><span data-stu-id="46830-430">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="46830-431">Razor Pages — это новый аспект платформы MVC ASP.NET Core, который делает создание кодов сценариев для страниц проще и эффективнее.</span><span class="sxs-lookup"><span data-stu-id="46830-431">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="46830-432">Если вам нужно руководство, использующее подход "модель-представление-контроллер", см. статью [Начало работы с MVC в ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="46830-432">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="46830-433">Этот документ содержит вводные сведения о Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="46830-433">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="46830-434">Это не пошаговое руководство.</span><span class="sxs-lookup"><span data-stu-id="46830-434">It's not a step by step tutorial.</span></span> <span data-ttu-id="46830-435">Если некоторые разделы покажутся вам слишком сложными, см. [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="46830-435">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="46830-436">Общие сведения об ASP.NET Core см. в разделе [Введение в ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="46830-436">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="46830-437">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="46830-437">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46830-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46830-438">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="46830-439">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46830-439">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46830-440">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="46830-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="46830-441">Создание проекта Razor Pages</span><span class="sxs-lookup"><span data-stu-id="46830-441">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46830-442">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46830-442">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46830-443">Подробные инструкции по созданию проекта Razor Pages см. в статье [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="46830-443">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46830-444">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="46830-444">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="46830-445">Из командной строки выполните команду `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="46830-445">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="46830-446">Откройте созданный файл *.csproj* в Visual Studio для Mac.</span><span class="sxs-lookup"><span data-stu-id="46830-446">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="46830-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46830-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="46830-448">Из командной строки выполните команду `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="46830-448">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="46830-449">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="46830-449">Razor Pages</span></span>

<span data-ttu-id="46830-450">Razor Pages активируется в файле *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="46830-450">Razor Pages is enabled in *Startup.cs* :</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="46830-451">Рассмотрим простую страницу: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="46830-451">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="46830-452">Приведенный выше код выглядит как [файл представления Razor](xref:tutorials/first-mvc-app/adding-view), используемый в приложениях ASP.NET Core с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="46830-452">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="46830-453">и отличается от него только директивой `@page`.</span><span class="sxs-lookup"><span data-stu-id="46830-453">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="46830-454">Директива `@page` превращает файл в действие MVC, а значит обрабатывает запросы напрямую, минуя контроллер.</span><span class="sxs-lookup"><span data-stu-id="46830-454">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="46830-455">`@page` должна быть первой директивой Razor на странице.</span><span class="sxs-lookup"><span data-stu-id="46830-455">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="46830-456">`@page` влияет на поведение всех остальных конструкций Razor.</span><span class="sxs-lookup"><span data-stu-id="46830-456">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="46830-457">Похожая страница с использованием класса `PageModel` показана в следующих двух файлах.</span><span class="sxs-lookup"><span data-stu-id="46830-457">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="46830-458">Файл *Pages/Index2.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-458">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="46830-459">Модель страницы *Pages/Index2.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="46830-459">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="46830-460">Как правило, файл класса `PageModel` называется так же, как файл Razor Pages, но с расширением *CS*.</span><span class="sxs-lookup"><span data-stu-id="46830-460">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="46830-461">Например, представленная выше страница Razor Pages называется *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-461">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="46830-462">Файл, содержащий класс `PageModel`, называется *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="46830-462">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="46830-463">Сопоставления URL-адресов со страницами определяются расположением конкретной страницы в файловой системе.</span><span class="sxs-lookup"><span data-stu-id="46830-463">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="46830-464">В приведенной ниже таблице показаны пути Razor Pages и соответствующие URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="46830-464">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="46830-465">Имя файла и путь</span><span class="sxs-lookup"><span data-stu-id="46830-465">File name and path</span></span>               | <span data-ttu-id="46830-466">Соответствующий URL</span><span class="sxs-lookup"><span data-stu-id="46830-466">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="46830-467">*/Pages/index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-467">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="46830-468">`/` или `/Index`</span><span class="sxs-lookup"><span data-stu-id="46830-468">`/` or `/Index`</span></span> |
| <span data-ttu-id="46830-469">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-469">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="46830-470">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-470">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="46830-471">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-471">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="46830-472">`/Store` или `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="46830-472">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="46830-473">Примечания.</span><span class="sxs-lookup"><span data-stu-id="46830-473">Notes:</span></span>

* <span data-ttu-id="46830-474">Среда выполнения по умолчанию ищет файлы Razor Pages в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="46830-474">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="46830-475">Если в URL-адресе не указана конкретная страница, по умолчанию открывается страница `Index`.</span><span class="sxs-lookup"><span data-stu-id="46830-475">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="46830-476">Создание простой формы</span><span class="sxs-lookup"><span data-stu-id="46830-476">Write a basic form</span></span>

<span data-ttu-id="46830-477">Razor Pages предназначена для упрощения реализации типовых шаблонов, которые используются в браузерах, при создании приложения.</span><span class="sxs-lookup"><span data-stu-id="46830-477">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="46830-478">[Привязки модели](xref:mvc/models/model-binding), [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и вспомогательные методы HTML *отлично работают* со свойствами, определенными в классе Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="46830-478">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="46830-479">Рассмотрим страницу с простой формой связи для модели `Contact`.</span><span class="sxs-lookup"><span data-stu-id="46830-479">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="46830-480">В представленных в этой статье примерах `DbContext` инициализируется в файле [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="46830-480">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="46830-481">Модель данных:</span><span class="sxs-lookup"><span data-stu-id="46830-481">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="46830-482">Контекст базы данных:</span><span class="sxs-lookup"><span data-stu-id="46830-482">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="46830-483">Файл представления *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-483">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="46830-484">Модель страницы *Pages/Create.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="46830-484">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="46830-485">Как правило, класс `PageModel` называется `<PageName>Model` и находится в том же пространстве имен, что и страница.</span><span class="sxs-lookup"><span data-stu-id="46830-485">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="46830-486">Класс `PageModel` позволяет разделять логику страницы и ее представление.</span><span class="sxs-lookup"><span data-stu-id="46830-486">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="46830-487">Он определяет обработчики страницы для запросов, отправляемых на страницу, а также данные для ее визуализации.</span><span class="sxs-lookup"><span data-stu-id="46830-487">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="46830-488">Это разделение позволяет:</span><span class="sxs-lookup"><span data-stu-id="46830-488">This separation allows:</span></span>

* <span data-ttu-id="46830-489">управлять зависимостями страниц с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="46830-489">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="46830-490">[Модульное тестирование](xref:test/razor-pages-tests) страниц.</span><span class="sxs-lookup"><span data-stu-id="46830-490">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="46830-491">Страница содержит *метод обработчика* `OnPostAsync`, который выполняется по запросам `POST` (когда пользователь публикует форму).</span><span class="sxs-lookup"><span data-stu-id="46830-491">The page has an `OnPostAsync` *handler method* , which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="46830-492">Методы обработчика можно добавить для любой HTTP-команды.</span><span class="sxs-lookup"><span data-stu-id="46830-492">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="46830-493">Наиболее распространенные обработчики</span><span class="sxs-lookup"><span data-stu-id="46830-493">The most common handlers are:</span></span>

* <span data-ttu-id="46830-494">`OnGet` — инициализация необходимого для страницы состояния.</span><span class="sxs-lookup"><span data-stu-id="46830-494">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="46830-495">Пример обработчика [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="46830-495">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="46830-496">`OnPost` — обработка отправленных через форму данных.</span><span class="sxs-lookup"><span data-stu-id="46830-496">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="46830-497">Суффикс `Async` не является обязательным, но часто используется для асинхронных функций.</span><span class="sxs-lookup"><span data-stu-id="46830-497">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="46830-498">Этот код типичен для Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="46830-498">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="46830-499">Если вы знакомы с приложениями ASP.NET, использующими контроллеры и представления:</span><span class="sxs-lookup"><span data-stu-id="46830-499">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="46830-500">Код `OnPostAsync` в предыдущем примере похож на стандартный код контроллера.</span><span class="sxs-lookup"><span data-stu-id="46830-500">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="46830-501">Большинство примитивов MVC, включая [привязку модели](xref:mvc/models/model-binding), [проверку](xref:mvc/models/validation), [проверку](xref:mvc/models/validation) и результаты действий, являются общими.</span><span class="sxs-lookup"><span data-stu-id="46830-501">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="46830-502">Предыдущий метод `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="46830-502">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="46830-503">Простая схема `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="46830-503">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="46830-504">Проверка на наличие ошибок проверки.</span><span class="sxs-lookup"><span data-stu-id="46830-504">Check for validation errors.</span></span>

* <span data-ttu-id="46830-505">Если ошибок нет, сохранение данных и перенаправление.</span><span class="sxs-lookup"><span data-stu-id="46830-505">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="46830-506">Если есть ошибки, отображение страницы с сообщениями проверки.</span><span class="sxs-lookup"><span data-stu-id="46830-506">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="46830-507">Проверка на стороне клиента выполняется точно так же, как в традиционных приложениях MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="46830-507">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="46830-508">Во многих случаях ошибки проверки выявляются на клиентском компьютере и на сервер не отправляются.</span><span class="sxs-lookup"><span data-stu-id="46830-508">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="46830-509">После успешного ввода данных метод обработчика `OnPostAsync` вызывает метод обработчика `RedirectToPage`, чтобы получить экземпляр `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="46830-509">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="46830-510">`RedirectToPage` — это новый результат действия, аналогичный `RedirectToAction` или `RedirectToRoute`, но настроенный для страниц.</span><span class="sxs-lookup"><span data-stu-id="46830-510">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="46830-511">В приведенном выше примере он выполняет перенаправление на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="46830-511">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="46830-512">Более подробно `RedirectToPage` рассматривается в разделе [Создание URL для страниц](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="46830-512">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="46830-513">Если в отправленной форме имеются ошибки проверки (переданные на сервер), метод обработчика `OnPostAsync` вызывает метод обработчика `Page`.</span><span class="sxs-lookup"><span data-stu-id="46830-513">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="46830-514">`Page` возвращает экземпляр `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="46830-514">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="46830-515">Возвращение `Page` аналогично тому, как действия в контроллерах возвращают `View`.</span><span class="sxs-lookup"><span data-stu-id="46830-515">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="46830-516">`PageResult` — тип возвращаемого значения по умолчанию для метода обработчика.</span><span class="sxs-lookup"><span data-stu-id="46830-516">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="46830-517">Метод обработчика, вернувший `void`, визуализирует страницу.</span><span class="sxs-lookup"><span data-stu-id="46830-517">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="46830-518">Для указания согласия на привязку модели в свойстве `Customer` используется атрибут `[BindProperty]`.</span><span class="sxs-lookup"><span data-stu-id="46830-518">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="46830-519">По умолчанию Razor Pages привязывает свойства ко всем командам, кроме `GET`.</span><span class="sxs-lookup"><span data-stu-id="46830-519">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="46830-520">Привязка к свойствам позволяет сократить объем необходимого кода.</span><span class="sxs-lookup"><span data-stu-id="46830-520">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="46830-521">Привязка уменьшает код за счет того, что для визуализации полей формы (`<input asp-for="Customer.Name">`) и получения входных данных используется одно и то же свойство.</span><span class="sxs-lookup"><span data-stu-id="46830-521">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="46830-522">Домашняя страница ( *Index.cshtml* ):</span><span class="sxs-lookup"><span data-stu-id="46830-522">The home page ( *Index.cshtml* ):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="46830-523">Связанный класс `PageModel` ( *Index.cshtml.cs* ):</span><span class="sxs-lookup"><span data-stu-id="46830-523">The associated `PageModel` class ( *Index.cshtml.cs* ):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="46830-524">Файл *Index.cshtml* содержит следующую разметку для создания ссылки на правку для каждого контактного лица.</span><span class="sxs-lookup"><span data-stu-id="46830-524">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="46830-525">[Вспомогательный тег привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` использовал атрибут `asp-route-{value}` для создания ссылки на страницу редактирования.</span><span class="sxs-lookup"><span data-stu-id="46830-525">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="46830-526">Эта ссылка содержит данные о маршруте с идентификатором контактного лица.</span><span class="sxs-lookup"><span data-stu-id="46830-526">The link contains route data with the contact ID.</span></span> <span data-ttu-id="46830-527">Например, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="46830-527">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="46830-528">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="46830-528">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="46830-529">Вспомогательные функции тегов включены с помощью `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="46830-529">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="46830-530">Файл *Pages/Edit.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-530">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="46830-531">Первая строка содержит директиву `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="46830-531">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="46830-532">Ограничение маршрутизации `"{id:int}"` указывает, что страница должна принимать обращенные к ней запросы, которые содержат данные о маршруте `int`.</span><span class="sxs-lookup"><span data-stu-id="46830-532">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="46830-533">Если запрос к странице не содержит данные о маршруте, которые можно конвертировать в `int`, среда выполнения возвращает ошибку HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="46830-533">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="46830-534">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="46830-534">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="46830-535">Файл *Pages/Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="46830-535">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="46830-536">Файл *Index.cshtml* также содержит разметку для создания кнопки удаления у каждого контакта клиента:</span><span class="sxs-lookup"><span data-stu-id="46830-536">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="46830-537">Во время обработки кнопки удаления в HTML ее `formaction` включает параметры для следующего:</span><span class="sxs-lookup"><span data-stu-id="46830-537">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="46830-538">Идентификатор контакта клиента, указанный атрибутом `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="46830-538">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="46830-539">Параметр `handler`, указанный атрибутом `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="46830-539">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="46830-540">Пример отображенной кнопки удаления с идентификатором контакта клиента `1`:</span><span class="sxs-lookup"><span data-stu-id="46830-540">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="46830-541">При выборе кнопки на сервер отправляется запрос формы `POST`.</span><span class="sxs-lookup"><span data-stu-id="46830-541">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="46830-542">По соглашению имя метода обработчика выбирается на основе значения параметра `handler` в соответствии со схемой `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="46830-542">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="46830-543">Так как `handler` — `delete` в этом примере, метод обработчика `OnPostDeleteAsync` используется для обработки запроса `POST`.</span><span class="sxs-lookup"><span data-stu-id="46830-543">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="46830-544">Если `asp-page-handler` имеет другое значение, например `remove`, выбирается метод обработчика с именем `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="46830-544">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="46830-545">В приведенном ниже коде показан обработчик `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="46830-545">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="46830-546">Метод `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="46830-546">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="46830-547">Принимает `id` из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="46830-547">Accepts the `id` from the query string.</span></span> <span data-ttu-id="46830-548">Если директива страницы *index.cshtml* содержит ограничение маршрутизации `"{id:int?}"`, то `id` будет получено из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="46830-548">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="46830-549">Данные маршрута для `id` указываются в универсальном коде ресурса (URI), например `https://localhost:5001/Customers/2`.</span><span class="sxs-lookup"><span data-stu-id="46830-549">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="46830-550">Отправляет в базу данных запрос контакта клиента с `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="46830-550">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="46830-551">Если контакт клиента найден, он удаляется из списка контактов.</span><span class="sxs-lookup"><span data-stu-id="46830-551">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="46830-552">База данных обновляется.</span><span class="sxs-lookup"><span data-stu-id="46830-552">The database is updated.</span></span>
* <span data-ttu-id="46830-553">Вызывает `RedirectToPage` для перенаправления на корневую страницу индекса (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="46830-553">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="46830-554">Маркировка свойств страницы как обязательных</span><span class="sxs-lookup"><span data-stu-id="46830-554">Mark page properties as required</span></span>

<span data-ttu-id="46830-555">Свойства класса `PageModel` можно отметить атрибутом [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute):</span><span class="sxs-lookup"><span data-stu-id="46830-555">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="46830-556">Дополнительные сведения см. в статье [Проверка модели](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="46830-556">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="46830-557">Обработка запросов HEAD с помощью вызова резервного обработчика OnGet</span><span class="sxs-lookup"><span data-stu-id="46830-557">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="46830-558">Запросы `HEAD` позволяют получать заголовки для определенного ресурса.</span><span class="sxs-lookup"><span data-stu-id="46830-558">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="46830-559">В отличие от запросов `GET` запросы `HEAD`не возвращают текст ответа.</span><span class="sxs-lookup"><span data-stu-id="46830-559">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="46830-560">Обработчик `OnHead` обычно создается и вызывается для выполнения запросов `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="46830-560">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="46830-561">Если в ASP.NET Core 2.1 или более поздней версии обработчик `OnHead` не определен, Razor Pages выполнит вызов резервного обработчика `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="46830-561">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="46830-562">Это поведение включается путем вызова [SetCompatibilityVersion](xref:mvc/compatibility-version) в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="46830-562">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="46830-563">Шаблоны по умолчанию создают вызов `SetCompatibilityVersion` в ASP.NET Core 2.1 и 2.2.</span><span class="sxs-lookup"><span data-stu-id="46830-563">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="46830-564">`SetCompatibilityVersion` задает для параметра `AllowMappingHeadRequestsToGetHandler` Razor Pages значение `true`.</span><span class="sxs-lookup"><span data-stu-id="46830-564">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="46830-565">Вместо включения всех возможных поведений с помощью метода `SetCompatibilityVersion` вы можете задать *конкретное* поведение.</span><span class="sxs-lookup"><span data-stu-id="46830-565">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="46830-566">Следующий код позволяет сопоставлять запросы `HEAD` с обработчиком `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="46830-566">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="46830-567">XSRF/CSRF и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="46830-567">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="46830-568">Вам не придется писать отдельный код для [проверки подлинности запросов](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="46830-568">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="46830-569">Razor Pages включает создание и проверку маркеров защиты от подделок по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="46830-569">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="46830-570">Использование макетов, частичных реплик, шаблонов и вспомогательных функций тегов с Razor Pages</span><span class="sxs-lookup"><span data-stu-id="46830-570">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="46830-571">Pages работает со всеми функциями подсистемы просмотра Razor.</span><span class="sxs-lookup"><span data-stu-id="46830-571">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="46830-572">Макеты, частичные реплики, шаблоны, вспомогательные функции тегов, а также файлы *_ViewStart.cshtml* и *_ViewImports.cshtml* работают точно так же, как и в стандартных представлениях Razor.</span><span class="sxs-lookup"><span data-stu-id="46830-572">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml* , *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="46830-573">Давайте упростим нашу страницу с помощью некоторых из этих функций.</span><span class="sxs-lookup"><span data-stu-id="46830-573">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="46830-574">Добавим [макет страницы](xref:mvc/views/layout) в файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-574">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml* :</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="46830-575">Этот [макет](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="46830-575">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="46830-576">управляет макетом каждой страницы (кроме страниц с отказом от макета);</span><span class="sxs-lookup"><span data-stu-id="46830-576">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="46830-577">импортирует HTML-структуры, такие как JavaScript и таблицы стилей.</span><span class="sxs-lookup"><span data-stu-id="46830-577">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="46830-578">Дополнительные сведения см. в статье о [макете](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="46830-578">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="46830-579">Свойство [Layout](xref:mvc/views/layout#specifying-a-layout) определяется в файле *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-579">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml* :</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="46830-580">Макет хранится в папке *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="46830-580">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="46830-581">Pages ищет другие представления (макеты, шаблоны, частичные реплики) в иерархическом порядке, начиная с той папки, где находится текущая страница.</span><span class="sxs-lookup"><span data-stu-id="46830-581">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="46830-582">Макет в папке *Pages/Shared* можно использовать на любой странице Razor, которая находится в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="46830-582">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="46830-583">Файл макета следует поместить в папку *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="46830-583">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="46830-584">Корпорация Майкрософт рекомендует **не** размещать файл макета в папке *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="46830-584">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="46830-585">*Views/Shared*  — это шаблон представлений MVC.</span><span class="sxs-lookup"><span data-stu-id="46830-585">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="46830-586">Razor Pages опирается на иерархию папок, а не на условные обозначения путей.</span><span class="sxs-lookup"><span data-stu-id="46830-586">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="46830-587">Поиск представлений в Razor Pages охватывает папку *Pages*.</span><span class="sxs-lookup"><span data-stu-id="46830-587">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="46830-588">Макеты, шаблоны и частичные реплики *работают* с контроллерами MVC и стандартными представлениями Razor.</span><span class="sxs-lookup"><span data-stu-id="46830-588">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="46830-589">Добавим файл *Pages/_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-589">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="46830-590">`@namespace` описывается далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="46830-590">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="46830-591">Директива `@addTagHelper` добавляет [встроенные вспомогательные теги](xref:mvc/views/tag-helpers/builtin-th/Index) на все страницы в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="46830-591">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="46830-592">Явное применение директивы `@namespace` на странице:</span><span class="sxs-lookup"><span data-stu-id="46830-592">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="46830-593">Данная директива задает пространство имен для страницы.</span><span class="sxs-lookup"><span data-stu-id="46830-593">The directive sets the namespace for the page.</span></span> <span data-ttu-id="46830-594">Включать пространство имен в директиву `@model` не требуется.</span><span class="sxs-lookup"><span data-stu-id="46830-594">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="46830-595">Если директива `@namespace` содержится в файле *_ViewImports.cshtml* , указанное пространство имен определяет префикс для созданного в Pages пространства имен, куда импортируется директива `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="46830-595">When the `@namespace` directive is contained in *_ViewImports.cshtml* , the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="46830-596">Остальная часть созданного пространства имен (суффикс) представляет собой разделенный точками относительный путь между папкой с файлом *_ViewImports.cshtml* и папкой, содержащей страницу.</span><span class="sxs-lookup"><span data-stu-id="46830-596">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="46830-597">Например, класс `PageModel` в файле *Pages/Customers/Edit.cshtml.cs* задает пространство имен явно.</span><span class="sxs-lookup"><span data-stu-id="46830-597">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="46830-598">Файл *Pages/_ViewImports.cshtml* задает следующее пространство имен:</span><span class="sxs-lookup"><span data-stu-id="46830-598">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="46830-599">Сформированное пространство имен для файла *Pages/Customers/Edit.cshtml* Razor Pages совпадает с пространством имен класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="46830-599">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="46830-600">`@namespace` *также работает со стандартными представлениями Razor.*</span><span class="sxs-lookup"><span data-stu-id="46830-600">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="46830-601">Исходный файл представления *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-601">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="46830-602">Обновленный файл представления *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46830-602">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="46830-603">[Начальный проект Razor Pages](#rpvs17) содержит файл *Pages/_ValidationScriptsPartial.cshtml* , который подключает проверку на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="46830-603">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml* , which hooks up client-side validation.</span></span>

<span data-ttu-id="46830-604">Дополнительные сведения о частичных представлениях см. в <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="46830-604">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="46830-605">Формирование URL-адресов для страниц</span><span class="sxs-lookup"><span data-stu-id="46830-605">URL generation for Pages</span></span>

<span data-ttu-id="46830-606">На представленной выше странице `Create` используется `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="46830-606">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="46830-607">Это приложение имеет следующую структуру файлов и папок:</span><span class="sxs-lookup"><span data-stu-id="46830-607">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="46830-608">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="46830-608">*/Pages*</span></span>

  * <span data-ttu-id="46830-609">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-609">*Index.cshtml*</span></span>
  * <span data-ttu-id="46830-610">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="46830-610">*/Customers*</span></span>

    * <span data-ttu-id="46830-611">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-611">*Create.cshtml*</span></span>
    * <span data-ttu-id="46830-612">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-612">*Edit.cshtml*</span></span>
    * <span data-ttu-id="46830-613">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="46830-613">*Index.cshtml*</span></span>

<span data-ttu-id="46830-614">После успешного выполнения страницы *Pages/Customers/Create.cshtml* и *Pages/Customers/Edit.cshtml* перенаправляются на страницу *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-614">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="46830-615">Строка `/Index` составляет часть URL-адреса для доступа к указанной выше странице.</span><span class="sxs-lookup"><span data-stu-id="46830-615">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="46830-616">Строка `/Index` позволяет создавать URL-адреса для страницы *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-616">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="46830-617">Пример:</span><span class="sxs-lookup"><span data-stu-id="46830-617">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="46830-618">Имя страницы — это путь к странице из корневой папки */Pages* , включая начальный символ `/` (например, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="46830-618">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="46830-619">Предыдущие образцы создания URL-адреса обеспечивают расширенные параметры и функциональные возможности по сравнению с жестким заданием URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="46830-619">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="46830-620">Формирование URL-адресов включает [маршрутизацию](xref:mvc/controllers/routing) и позволяет генерировать и включать в код параметры в зависимости от того, как определяется маршрут в пути назначения.</span><span class="sxs-lookup"><span data-stu-id="46830-620">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="46830-621">Формирование URL-адресов для страниц поддерживает относительные имена.</span><span class="sxs-lookup"><span data-stu-id="46830-621">URL generation for pages supports relative names.</span></span> <span data-ttu-id="46830-622">В приведенной ниже таблице показано, какая страница индекса выбирается с каждым параметром `RedirectToPage` в *Pages/Customers/Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46830-622">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml* :</span></span>

| <span data-ttu-id="46830-623">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="46830-623">RedirectToPage(x)</span></span>| <span data-ttu-id="46830-624">Страница</span><span class="sxs-lookup"><span data-stu-id="46830-624">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="46830-625">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="46830-625">RedirectToPage("/Index")</span></span> | <span data-ttu-id="46830-626">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="46830-626">*Pages/Index*</span></span> |
| <span data-ttu-id="46830-627">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="46830-627">RedirectToPage("./Index");</span></span> | <span data-ttu-id="46830-628">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="46830-628">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="46830-629">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="46830-629">RedirectToPage("../Index")</span></span> | <span data-ttu-id="46830-630">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="46830-630">*Pages/Index*</span></span> |
| <span data-ttu-id="46830-631">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="46830-631">RedirectToPage("Index")</span></span>  | <span data-ttu-id="46830-632">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="46830-632">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="46830-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")` и `RedirectToPage("../Index")` — это *относительные имена*.</span><span class="sxs-lookup"><span data-stu-id="46830-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="46830-634">Для получения имени целевой страницы параметр `RedirectToPage`*комбинируется* с путем текущей страницы.</span><span class="sxs-lookup"><span data-stu-id="46830-634">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="46830-635">Привязка относительных имен полезна при создании сайтов со сложной структурой.</span><span class="sxs-lookup"><span data-stu-id="46830-635">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="46830-636">Если для связи между страницами в определенной папке используются относительные имена, эту папку можно переименовать.</span><span class="sxs-lookup"><span data-stu-id="46830-636">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="46830-637">При этом все ссылки останутся рабочими (так как не включают имя папки).</span><span class="sxs-lookup"><span data-stu-id="46830-637">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="46830-638">Чтобы выполнить перенаправление на страницу в другой [области](xref:mvc/controllers/areas), укажите эту область:</span><span class="sxs-lookup"><span data-stu-id="46830-638">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="46830-639">Для получения дополнительной информации см. <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="46830-639">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="46830-640">Атрибут ViewData</span><span class="sxs-lookup"><span data-stu-id="46830-640">ViewData attribute</span></span>

<span data-ttu-id="46830-641">Данные могут передаваться на страницу с помощью атрибута [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="46830-641">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="46830-642">Свойства в контроллерах или моделях Razor Page, отмеченные атрибутом `[ViewData]`, обладают собственными значениями, загружаемыми из [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="46830-642">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="46830-643">В следующем примере класс `AboutModel` содержит свойство `Title`, отмеченное атрибутом `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="46830-643">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="46830-644">Свойство `Title` задает заголовок страницы About.</span><span class="sxs-lookup"><span data-stu-id="46830-644">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="46830-645">На странице About доступ к свойству `Title` осуществляется как доступ к свойству модели.</span><span class="sxs-lookup"><span data-stu-id="46830-645">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="46830-646">В макете заголовок считывается из словаря ViewData.</span><span class="sxs-lookup"><span data-stu-id="46830-646">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="46830-647">TempData</span><span class="sxs-lookup"><span data-stu-id="46830-647">TempData</span></span>

<span data-ttu-id="46830-648">ASP.NET Core позволяет использовать свойство [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) в [контроллере](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="46830-648">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="46830-649">Это свойство хранит данные до тех пор, пока они не будут прочитаны.</span><span class="sxs-lookup"><span data-stu-id="46830-649">This property stores data until it's read.</span></span> <span data-ttu-id="46830-650">Для проверки данных без удаления можно использовать методы `Keep` и `Peek`.</span><span class="sxs-lookup"><span data-stu-id="46830-650">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="46830-651">`TempData` удобно использовать для перенаправления, когда данные требуются больше чем для одного запроса.</span><span class="sxs-lookup"><span data-stu-id="46830-651">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="46830-652">В следующем коде значение `Message` задается с помощью `TempData`:</span><span class="sxs-lookup"><span data-stu-id="46830-652">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="46830-653">Представленная ниже разметка в файле *Pages/Customers/Index.cshtml* отображает значение `Message` с помощью `TempData`.</span><span class="sxs-lookup"><span data-stu-id="46830-653">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="46830-654">Модель страницы *Pages/Customers/Index.cshtml.cs* применяет атрибут `[TempData]` к свойству `Message`.</span><span class="sxs-lookup"><span data-stu-id="46830-654">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="46830-655">Дополнительные сведения см. в разделе [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="46830-655">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="46830-656">Несколько обработчиков на страницу</span><span class="sxs-lookup"><span data-stu-id="46830-656">Multiple handlers per page</span></span>

<span data-ttu-id="46830-657">Следующая страница формирует разметку для двух обработчиков с помощью вспомогательной функции тегов `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="46830-657">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="46830-658">Форма в предыдущем примере включает две кнопки отправки, каждая из которых отправляет данные на отдельный URL-адрес с помощью `FormActionTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="46830-658">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="46830-659">Атрибут `asp-page-handler` является дополнением к `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="46830-659">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="46830-660">Атрибут `asp-page-handler` формирует URL-адреса, ,которые используются для отправки данных в каждый из методов обработчиков, определенных страницей.</span><span class="sxs-lookup"><span data-stu-id="46830-660">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="46830-661">`asp-page` не задается, так как пример сопоставлен с текущей страницей.</span><span class="sxs-lookup"><span data-stu-id="46830-661">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="46830-662">Модель страницы</span><span class="sxs-lookup"><span data-stu-id="46830-662">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="46830-663">В представленном выше коде используются *именованные методы обработчика*.</span><span class="sxs-lookup"><span data-stu-id="46830-663">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="46830-664">Именованные методы обработчика создаются путем размещения определенного текста в имени после `On<HTTP Verb>` и перед `Async` (если есть).</span><span class="sxs-lookup"><span data-stu-id="46830-664">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="46830-665">В приведенном выше примере использовались методы страницы OnPost **JoinList** Async и OnPost **JoinListUC** Async.</span><span class="sxs-lookup"><span data-stu-id="46830-665">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="46830-666">Если убрать *OnPost* и *Async* , имена обработчиков будут выглядеть как `JoinList` и `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="46830-666">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="46830-667">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="46830-667">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="46830-668">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="46830-668">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="46830-669">Пользовательские маршруты</span><span class="sxs-lookup"><span data-stu-id="46830-669">Custom routes</span></span>

<span data-ttu-id="46830-670">С помощью директивы `@page` можно сделать следующее.</span><span class="sxs-lookup"><span data-stu-id="46830-670">Use the `@page` directive to:</span></span>

* <span data-ttu-id="46830-671">Указать пользовательский маршрут к странице.</span><span class="sxs-lookup"><span data-stu-id="46830-671">Specify a custom route to a page.</span></span> <span data-ttu-id="46830-672">Например, можно задать маршрут к странице "Сведения" `/Some/Other/Path`: `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="46830-672">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="46830-673">Добавить сегменты к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="46830-673">Append segments to a page's default route.</span></span> <span data-ttu-id="46830-674">Например, к такому маршруту можно добавить сегмент item: `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="46830-674">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="46830-675">Добавить параметры к маршруту страницы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="46830-675">Append parameters to a page's default route.</span></span> <span data-ttu-id="46830-676">Например, для страницы с `@page "{id}"` может потребоваться параметр идентификатора `id`.</span><span class="sxs-lookup"><span data-stu-id="46830-676">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="46830-677">Поддерживается путь относительно корня, заданный знаком тильды (`~`) в начале пути.</span><span class="sxs-lookup"><span data-stu-id="46830-677">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="46830-678">Например, `@page "~/Some/Other/Path"` равносильно `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="46830-678">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="46830-679">Если вы не хотите, чтобы в URL-адресе отображалась строка запроса `?handler=JoinList`, измените маршрут так, чтобы в качестве пути в URL-адресе указывалось имя обработчика.</span><span class="sxs-lookup"><span data-stu-id="46830-679">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="46830-680">Для настройки маршрута добавьте после директивы `@page` шаблон маршрута, заключенный в двойные кавычки.</span><span class="sxs-lookup"><span data-stu-id="46830-680">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="46830-681">При использовании представленного выше кода URL-путь для отправки данных в `OnPostJoinListAsync` будет выглядеть как `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="46830-681">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="46830-682">URL-путь для отправки данных в `OnPostJoinListUCAsync` будет иметь вид `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="46830-682">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="46830-683">Символ `?` после `handler` означает, что параметр маршрута является необязательным.</span><span class="sxs-lookup"><span data-stu-id="46830-683">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="46830-684">Конфигурация и параметры</span><span class="sxs-lookup"><span data-stu-id="46830-684">Configuration and settings</span></span>

<span data-ttu-id="46830-685">Чтобы настроить дополнительные параметры, воспользуйтесь методом расширения `AddRazorPagesOptions` в построителе MVC:</span><span class="sxs-lookup"><span data-stu-id="46830-685">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="46830-686">В настоящее время `RazorPagesOptions` позволяет задавать корневую папку для страниц и добавлять для них обозначения моделей приложений.</span><span class="sxs-lookup"><span data-stu-id="46830-686">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="46830-687">В дальнейшем мы расширим спектр его возможностей.</span><span class="sxs-lookup"><span data-stu-id="46830-687">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="46830-688">Сведения о предварительной компиляции представлений см. на странице [Компиляция представлений Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="46830-688">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="46830-689">[Загрузить или просмотреть пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="46830-689">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="46830-690">Дополнительные общие сведения см. на странице [Учебник. Начало работы с Razor Pages в ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="46830-690">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="46830-691">Указание местонахождения Razor Pages в корне каталога</span><span class="sxs-lookup"><span data-stu-id="46830-691">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="46830-692">По умолчанию Razor Pages находится в корне каталога */Pages*.</span><span class="sxs-lookup"><span data-stu-id="46830-692">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="46830-693">Добавьте [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) в [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_), чтобы указать, что Razor Pages находится в [корневой папке содержимого](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) приложения:</span><span class="sxs-lookup"><span data-stu-id="46830-693">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="46830-694">Указание местонахождения Razor Pages в пользовательском корневом каталоге</span><span class="sxs-lookup"><span data-stu-id="46830-694">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="46830-695">Добавьте [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) в [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_), чтобы указать, что Razor Pages находится в пользовательской корневой папке приложения (укажите относительный путь):</span><span class="sxs-lookup"><span data-stu-id="46830-695">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="46830-696">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="46830-696">Additional resources</span></span>

* [<span data-ttu-id="46830-697">Атрибут authorize и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="46830-697">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
