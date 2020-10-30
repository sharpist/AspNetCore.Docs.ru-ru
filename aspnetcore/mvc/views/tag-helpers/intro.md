---
title: Вспомогательные функции тегов в ASP.NET Core
author: rick-anderson
description: Сведения о вспомогательных функциях тегов и их использовании в ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 781365d99c6d36d8abaec9681128ba712db8cb88
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060668"
---
# <a name="tag-helpers-in-aspnet-core"></a><span data-ttu-id="28f8d-103">Вспомогательные функции тегов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28f8d-103">Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="28f8d-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="28f8d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="28f8d-105">Что собой представляют вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="28f8d-105">What are Tag Helpers</span></span>

<span data-ttu-id="28f8d-106">Вспомогательные функции тегов позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="28f8d-106">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="28f8d-107">Например, встроенная функция `ImageTagHelper` может добавить номер версии к имени изображения.</span><span class="sxs-lookup"><span data-stu-id="28f8d-107">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="28f8d-108">При каждом изменении изображения сервер генерирует новую уникальную версию изображения, поэтому клиенты гарантированно получат текущую версию изображения (вместо устаревшего кэшированного изображения).</span><span class="sxs-lookup"><span data-stu-id="28f8d-108">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="28f8d-109">Существует множество встроенных вспомогательных функций тегов для общих задач — например, для создания форм, ссылок, загрузки ресурсов и т. д. Кроме того, огромное количество функций доступно в общедоступных репозиториях GitHub и в качестве пакетов NuGet.</span><span class="sxs-lookup"><span data-stu-id="28f8d-109">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="28f8d-110">Вспомогательные функции тегов разрабатываются на C# и предназначены для HTML-элементов на основе имени элемента, имени атрибута или родительского тега.</span><span class="sxs-lookup"><span data-stu-id="28f8d-110">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="28f8d-111">Например, встроенная функция `LabelTagHelper` может работать с HTML-элементом `<label>`, если применены атрибуты `LabelTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="28f8d-111">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="28f8d-112">Если вы знакомы со [вспомогательными](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers)модулями HTML, вспомогательные функции тегов уменьшают явные переходы между HTML и C# в :::no-loc(Razor)::: представлениях.</span><span class="sxs-lookup"><span data-stu-id="28f8d-112">If you're familiar with [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in :::no-loc(Razor)::: views.</span></span> <span data-ttu-id="28f8d-113">Во многих случаях вспомогательные методы HTML располагают альтернативными вариантами для определенной вспомогательной функции тега, но следует отметить, что вспомогательные функции тегов не заменяют вспомогательные методы HTML и для каждого вспомогательного метода HTML не существует конкретной вспомогательной функции тега.</span><span class="sxs-lookup"><span data-stu-id="28f8d-113">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="28f8d-114">Более подробно различия описаны в разделе о [сравнении вспомогательных методов HTML и вспомогательных функций тегов](#tag-helpers-compared-to-html-helpers).</span><span class="sxs-lookup"><span data-stu-id="28f8d-114">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="28f8d-115">Возможности и преимущества вспомогательных функций тегов</span><span class="sxs-lookup"><span data-stu-id="28f8d-115">What Tag Helpers provide</span></span>

<span data-ttu-id="28f8d-116">**Удобство разработки с поддержкой HTML** В большинстве случаев :::no-loc(Razor)::: Разметка, использующая вспомогательные функции тегов, выглядит как стандартный HTML.</span><span class="sxs-lookup"><span data-stu-id="28f8d-116">**An HTML-friendly development experience** For the most part, :::no-loc(Razor)::: markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="28f8d-117">Интерфейсные конструкторы, поддерживающие HTML/CSS/JavaScript, могут редактировать :::no-loc(Razor)::: без изучения :::no-loc(Razor)::: синтаксиса C#.</span><span class="sxs-lookup"><span data-stu-id="28f8d-117">Front-end designers conversant with HTML/CSS/JavaScript can edit :::no-loc(Razor)::: without learning C# :::no-loc(Razor)::: syntax.</span></span>

<span data-ttu-id="28f8d-118">**Широкая среда IntelliSense для создания HTML и :::no-loc(Razor)::: разметки** , она имеет четкий контраст для вспомогательных функций HTML, предыдущий подход к созданию разметки в представлениях на стороне сервера :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="28f8d-118">**A rich IntelliSense environment for creating HTML and :::no-loc(Razor)::: markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in :::no-loc(Razor)::: views.</span></span> <span data-ttu-id="28f8d-119">Более подробно различия описаны в разделе о [сравнении вспомогательных методов HTML и вспомогательных функций тегов](#tag-helpers-compared-to-html-helpers).</span><span class="sxs-lookup"><span data-stu-id="28f8d-119">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="28f8d-120">Возможности среды IntelliSense объясняются в статье [Поддержка IntelliSense для вспомогательных функций тегов](#intellisense-support-for-tag-helpers).</span><span class="sxs-lookup"><span data-stu-id="28f8d-120">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="28f8d-121">Даже разработчики, имеющие опыт работы с :::no-loc(Razor)::: синтаксисом C#, более продуктивно используют вспомогательные функции тегов, чем написание :::no-loc(Razor)::: разметки C#.</span><span class="sxs-lookup"><span data-stu-id="28f8d-121">Even developers experienced with :::no-loc(Razor)::: C# syntax are more productive using Tag Helpers than writing C# :::no-loc(Razor)::: markup.</span></span>

<span data-ttu-id="28f8d-122">**Вы сможете работать более продуктивно и создавать более надежный, устойчивый и безопасный код, используя информацию, которая доступна только на сервере** . Например, когда ранее требовалось изменить изображение, нужно было изменить имя изображения.</span><span class="sxs-lookup"><span data-stu-id="28f8d-122">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="28f8d-123">Изображения нужно было обязательно кэшировать по причинам эффективности, и до тех пор, пока не менялось имя изображения, клиенты могли получать его устаревшую копию.</span><span class="sxs-lookup"><span data-stu-id="28f8d-123">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="28f8d-124">Исторически после редактирования изображения нужно было обязательно менять его имя и обновлять каждую ссылку на него в веб приложении.</span><span class="sxs-lookup"><span data-stu-id="28f8d-124">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="28f8d-125">Это не только очень трудоемкое, но и подверженное ошибкам (можно пропустить ссылку, случайно ввести неправильную строку и т. д.). Встроенное `ImageTagHelper` действие может сделать это автоматически.</span><span class="sxs-lookup"><span data-stu-id="28f8d-125">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="28f8d-126">Функция `ImageTagHelper` добавляет номер версии к имени изображения, поэтому при каждом изменении изображения сервер автоматически генерирует новую уникальную версию изображения.</span><span class="sxs-lookup"><span data-stu-id="28f8d-126">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="28f8d-127">Клиенты гарантированно получают текущее изображение.</span><span class="sxs-lookup"><span data-stu-id="28f8d-127">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="28f8d-128">Все это достигается с помощью функции `ImageTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="28f8d-128">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="28f8d-129">Большинство встроенных вспомогательных функций тегов работают со стандартными HTML-элементами и предоставляют для них атрибуты на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="28f8d-129">Most built-in Tag Helpers target standard HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="28f8d-130">Например, элемент `<input>`, используемый во многих представлениях в папке *Представления/учетная запись* , содержит атрибут `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="28f8d-130">For example, the `<input>` element used in many views in the *Views/Account* folder contains the `asp-for` attribute.</span></span> <span data-ttu-id="28f8d-131">Этот атрибут извлекает имя свойства указанной модели в готовый для просмотра HTML-код.</span><span class="sxs-lookup"><span data-stu-id="28f8d-131">This attribute extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="28f8d-132">Рассмотрим :::no-loc(Razor)::: представление со следующей моделью:</span><span class="sxs-lookup"><span data-stu-id="28f8d-132">Consider a :::no-loc(Razor)::: view with the following model:</span></span>

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

<span data-ttu-id="28f8d-133">Следующая :::no-loc(Razor)::: Разметка:</span><span class="sxs-lookup"><span data-stu-id="28f8d-133">The following :::no-loc(Razor)::: markup:</span></span>

```cshtml
<label asp-for="Movie.Title"></label>
```

<span data-ttu-id="28f8d-134">Генерирует следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="28f8d-134">Generates the following HTML:</span></span>

```html
<label for="Movie_Title">Title</label>
```

<span data-ttu-id="28f8d-135">Атрибут `asp-for` предоставляется свойством `For` в [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="28f8d-135">The `asp-for` attribute is made available by the `For` property in the [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span></span> <span data-ttu-id="28f8d-136">Дополнительные сведения см. в статье [Создание вспомогательных функций тегов](xref:mvc/views/tag-helpers/authoring).</span><span class="sxs-lookup"><span data-stu-id="28f8d-136">See [Author Tag Helpers](xref:mvc/views/tag-helpers/authoring) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="28f8d-137">Управление областью действия вспомогательной функции тега</span><span class="sxs-lookup"><span data-stu-id="28f8d-137">Managing Tag Helper scope</span></span>

<span data-ttu-id="28f8d-138">Управление областью действия вспомогательных функций тегов осуществляется с помощью сочетания `@addTagHelper`, `@removeTagHelper` и символа отказа "!".</span><span class="sxs-lookup"><span data-stu-id="28f8d-138">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="28f8d-139">Директива `@addTagHelper` обеспечивает доступность вспомогательных функций тегов</span><span class="sxs-lookup"><span data-stu-id="28f8d-139">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="28f8d-140">При создании веб-приложения ASP.NET Core с именем *AuthoringTagHelpers* в проект будет добавлен следующий файл *Views/_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="28f8d-140">If you create a new ASP.NET Core web app named *AuthoringTagHelpers* , the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="28f8d-141">Директива `@addTagHelper` делает вспомогательные функции тегов доступными в представлении.</span><span class="sxs-lookup"><span data-stu-id="28f8d-141">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="28f8d-142">В этом случае файл представления имеет вид *pages/_ViewImports. cshtml* , который по умолчанию наследуется всеми файлами в папке *pages* и вложенными папками. обеспечение доступности вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="28f8d-142">In this case, the view file is *Pages/_ViewImports.cshtml* , which by default is inherited by all files in the *Pages* folder and subfolders; making Tag Helpers available.</span></span> <span data-ttu-id="28f8d-143">В приведенном выше коде используется синтаксис с подстановочными знаками (" \* "), указывающий, что все вспомогательные функции тегов в указанной сборке ( *Microsoft. AspNetCore. MVC. TagHelpers* ) будут доступны для каждого файла представления в каталоге *views* или подкаталоге представлений.</span><span class="sxs-lookup"><span data-stu-id="28f8d-143">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly ( *Microsoft.AspNetCore.Mvc.TagHelpers* ) will be available to every view file in the *Views* directory or subdirectory.</span></span> <span data-ttu-id="28f8d-144">Первый параметр после директивы `@addTagHelper` указывает загружаемые вспомогательные функции тегов (мы используем "\*" для всех вспомогательных функций тегов), а второй параметр "Microsoft.AspNetCore.Mvc.TagHelpers" указывает сборку, содержащую вспомогательные функции тегов.</span><span class="sxs-lookup"><span data-stu-id="28f8d-144">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="28f8d-145">*Microsoft.AspNetCore.Mvc.TagHelpers* — это сборка для встроенных вспомогательных функций тегов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28f8d-145">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="28f8d-146">Чтобы использовать все вспомогательные функции тегов в этом проекте (где создается сборка *AuthoringTagHelpers* ), нужно сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="28f8d-146">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers* ), you would use the following:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="28f8d-147">Если проект содержит `EmailTagHelper` с пространством имен по умолчанию (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), можно указать полное имя (FQN) вспомогательной функции тега:</span><span class="sxs-lookup"><span data-stu-id="28f8d-147">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="28f8d-148">Чтобы добавить вспомогательную функцию тега в представление с помощью FQN, сначала добавьте FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), а затем — имя сборки ( *AuthoringTagHelpers* ).</span><span class="sxs-lookup"><span data-stu-id="28f8d-148">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name ( *AuthoringTagHelpers* ).</span></span> <span data-ttu-id="28f8d-149">Большинство разработчиков предпочитают использовать синтаксис знаков подстановки "\*".</span><span class="sxs-lookup"><span data-stu-id="28f8d-149">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="28f8d-150">Синтаксис знаков подстановки позволяет вставлять знак подстановки "\*" в FQN в качестве суффикса.</span><span class="sxs-lookup"><span data-stu-id="28f8d-150">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="28f8d-151">Например, любая из следующих директив добавит `EmailTagHelper`:</span><span class="sxs-lookup"><span data-stu-id="28f8d-151">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="28f8d-152">Как упоминалось ранее, добавление `@addTagHelper` директивы в файл *views/_ViewImports. cshtml* делает вспомогательную функцию тега доступной для всех файлов представлений в каталоге *views* и подкаталогах.</span><span class="sxs-lookup"><span data-stu-id="28f8d-152">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and subdirectories.</span></span> <span data-ttu-id="28f8d-153">Директиву `@addTagHelper` можно использовать в конкретных файлах представлений, если вы хотите, чтобы вспомогательная функция тега находилась только в этих представлениях.</span><span class="sxs-lookup"><span data-stu-id="28f8d-153">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="28f8d-154">Директива `@removeTagHelper` удаляет вспомогательные функций тегов</span><span class="sxs-lookup"><span data-stu-id="28f8d-154">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="28f8d-155">У директивы `@removeTagHelper` есть те же два параметра, что и у директивы `@addTagHelper`, и она удаляет ранее добавленную вспомогательную функцию тега.</span><span class="sxs-lookup"><span data-stu-id="28f8d-155">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="28f8d-156">Например, если применить директиву `@removeTagHelper` к определенному представлению, она удалит из него указанную вспомогательную функцию тега.</span><span class="sxs-lookup"><span data-stu-id="28f8d-156">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="28f8d-157">Использование директивы `@removeTagHelper` в файле *Views/Folder/_ViewImports.cshtml* приводит к удалению указанной вспомогательной функции тега из всех представлений в *папке* .</span><span class="sxs-lookup"><span data-stu-id="28f8d-157">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder* .</span></span>

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a><span data-ttu-id="28f8d-158">Контроль области действия вспомогательной функции тега с помощью файла *_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="28f8d-158">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="28f8d-159">Можно добавить файл *_ViewImports.cshtml* в любую папку представления, и подсистема представлений применит директивы из этого файла и файла *Views/_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="28f8d-159">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="28f8d-160">Если вы добавили пустой файл *Views/Home/_ViewImports.cshtml* для представлений *Home* , ничего не изменится, поскольку файл *_ViewImports.cshtml* является дополнительным.</span><span class="sxs-lookup"><span data-stu-id="28f8d-160">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="28f8d-161">Любая директива `@addTagHelper`, добавляемая в файл *Views/Home/_ViewImports.cshtml* (и не входящая в файл по умолчанию *Views/_ViewImports.cshtml* ), будет предоставлять эти вспомогательные функции тегов для представлений только в папке *Home* .</span><span class="sxs-lookup"><span data-stu-id="28f8d-161">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="28f8d-162">Отказ от использования отдельных элементов</span><span class="sxs-lookup"><span data-stu-id="28f8d-162">Opting out of individual elements</span></span>

<span data-ttu-id="28f8d-163">Можно отключить вспомогательную функцию тега на уровне элемента с помощью символа отказа от использования ("!").</span><span class="sxs-lookup"><span data-stu-id="28f8d-163">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="28f8d-164">Например, с помощью данного символа отключена проверка `Email` в `<span>`:</span><span class="sxs-lookup"><span data-stu-id="28f8d-164">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="28f8d-165">Символ отказа от использования вспомогательной функции тега необходимо применять в открывающем и закрывающем тегах.</span><span class="sxs-lookup"><span data-stu-id="28f8d-165">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="28f8d-166">(Редактор Visual Studio автоматически добавляет символ отказа в закрывающий тег, если такой символ добавлен в открывающий тег.)</span><span class="sxs-lookup"><span data-stu-id="28f8d-166">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="28f8d-167">После добавления символа отказа атрибуты элемента и вспомогательной функции больше не будут отображаться отдельным шрифтом.</span><span class="sxs-lookup"><span data-stu-id="28f8d-167">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="28f8d-168">Применение `@tagHelperPrefix` для явного использования вспомогательной функции тега</span><span class="sxs-lookup"><span data-stu-id="28f8d-168">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="28f8d-169">Директива `@tagHelperPrefix` позволяет указать строку префикса тега для включения поддержки вспомогательной функции тега и явного использования этой функции.</span><span class="sxs-lookup"><span data-stu-id="28f8d-169">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="28f8d-170">Например, можно добавить следующую разметку в файл *Views/_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="28f8d-170">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```cshtml
@tagHelperPrefix th:
```

<span data-ttu-id="28f8d-171">На приведенном ниже рисунке кода задан префикс `th:` вспомогательной функции тега, поэтому вспомогательную функцию тега поддерживают только элементы с префиксом `th:` (элементы с поддержкой вспомогательной функции тега выделены особым шрифтом).</span><span class="sxs-lookup"><span data-stu-id="28f8d-171">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="28f8d-172">У элементов `<label>` и `<input>` есть префикс вспомогательной функции тега и поддержка этой функции, а у элемента `<span>` этого префикса и поддержки нет.</span><span class="sxs-lookup"><span data-stu-id="28f8d-172">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element doesn't.</span></span>

![Изображение](intro/_static/thp.png)

<span data-ttu-id="28f8d-174">Правила иерархии, которые применяются к `@addTagHelper`, также применяются и к `@tagHelperPrefix`.</span><span class="sxs-lookup"><span data-stu-id="28f8d-174">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="self-closing-tag-helpers"></a><span data-ttu-id="28f8d-175">Самозакрывающиеся вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="28f8d-175">Self-closing Tag Helpers</span></span>

<span data-ttu-id="28f8d-176">Многие вспомогательные функции тегов нельзя использовать как самозакрывающиеся теги.</span><span class="sxs-lookup"><span data-stu-id="28f8d-176">Many Tag Helpers can't be used as self-closing tags.</span></span> <span data-ttu-id="28f8d-177">Некоторые вспомогательные функции тегов созданы как самозакрывающиеся теги.</span><span class="sxs-lookup"><span data-stu-id="28f8d-177">Some Tag Helpers are designed to be self-closing tags.</span></span> <span data-ttu-id="28f8d-178">При использовании вспомогательной функции тегов, которая не создана как самозакрывающаяся, подавляются выводимые данные.</span><span class="sxs-lookup"><span data-stu-id="28f8d-178">Using a Tag Helper that was not designed to be self-closing suppresses the rendered output.</span></span> <span data-ttu-id="28f8d-179">При самостоятельном закрывании вспомогательной функции тегов в выводимых данных появляется самозакрывающийся тег.</span><span class="sxs-lookup"><span data-stu-id="28f8d-179">Self-closing a Tag Helper results in a self-closing tag in the rendered output.</span></span> <span data-ttu-id="28f8d-180">Дополнительные сведения см. в [этом примечании](xref:mvc/views/tag-helpers/authoring#self-closing) в статье [Создание вспомогательных функций тегов](xref:mvc/views/tag-helpers/authoring).</span><span class="sxs-lookup"><span data-stu-id="28f8d-180">For more information, see [this note](xref:mvc/views/tag-helpers/authoring#self-closing) in [Authoring Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span></span>

## <a name="c-in-tag-helpers-attributedeclaration"></a><span data-ttu-id="28f8d-181">C# в атрибуте или объявлении вспомогательных функций тегов</span><span class="sxs-lookup"><span data-stu-id="28f8d-181">C# in Tag Helpers attribute/declaration</span></span> 

<span data-ttu-id="28f8d-182">Вспомогательные функции тегов не разрешают применять C# в области объявления атрибута или тега элемента.</span><span class="sxs-lookup"><span data-stu-id="28f8d-182">Tag Helpers do not allow C# in the element's attribute or tag declaration area.</span></span> <span data-ttu-id="28f8d-183">Например, приведенный ниже код недопустим:</span><span class="sxs-lookup"><span data-stu-id="28f8d-183">For example, the following code is not valid:</span></span>

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

<span data-ttu-id="28f8d-184">Приведенный выше код можно написать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="28f8d-184">The preceding code can be written as:</span></span>

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="28f8d-185">Поддержка Intellisense для вспомогательных функций тегов</span><span class="sxs-lookup"><span data-stu-id="28f8d-185">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="28f8d-186">При создании нового веб-приложения ASP.NET Core в Visual Studio добавляется пакет NuGet Microsoft. :::no-loc(Razor)::: AspNetCore. Средства».</span><span class="sxs-lookup"><span data-stu-id="28f8d-186">When you create a new ASP.NET Core web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.:::no-loc(Razor):::.Tools".</span></span> <span data-ttu-id="28f8d-187">Это пакет добавляет инструментарий вспомогательной функции тега.</span><span class="sxs-lookup"><span data-stu-id="28f8d-187">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="28f8d-188">Рекомендуется написать элемент HTML `<label>`.</span><span class="sxs-lookup"><span data-stu-id="28f8d-188">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="28f8d-189">Когда вы введете `<l` в редакторе Visual Studio, IntelliSense отобразит подходящие элементы:</span><span class="sxs-lookup"><span data-stu-id="28f8d-189">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![Изображение](intro/_static/label.png)

<span data-ttu-id="28f8d-191">Выводится не только справка по HTML, но и значок ("@" symbol with "<>" под ней).</span><span class="sxs-lookup"><span data-stu-id="28f8d-191">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![Изображение](intro/_static/tagSym.png)

<span data-ttu-id="28f8d-193">Он показывает элемент, на который нацелены вспомогательные функции тегов.</span><span class="sxs-lookup"><span data-stu-id="28f8d-193">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="28f8d-194">Чистые элементы HTML (например, `fieldset`) отображают значок "<>".</span><span class="sxs-lookup"><span data-stu-id="28f8d-194">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="28f8d-195">Чистый тег HTML `<label>` отображается (с помощью базовой цветовой гаммы Visual Studio) коричневым цветом, атрибуты — красным, а значения атрибутов — синим.</span><span class="sxs-lookup"><span data-stu-id="28f8d-195">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![Изображение](intro/_static/LableHtmlTag.png)

<span data-ttu-id="28f8d-197">После того как вы введете `<label`, IntelliSense выведет перечень доступных атрибутов HTML и CSS и атрибутов для вспомогательной функции тега:</span><span class="sxs-lookup"><span data-stu-id="28f8d-197">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![Изображение](intro/_static/labelattr.png)

<span data-ttu-id="28f8d-199">Функция завершения операторов IntelliSense позволяет заполнить выражение выбранным значением с помощью клавиши TAB:</span><span class="sxs-lookup"><span data-stu-id="28f8d-199">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![Изображение](intro/_static/stmtcomplete.png)

<span data-ttu-id="28f8d-201">После ввода атрибута вспомогательной функции тега шрифты тега и атрибута изменяются.</span><span class="sxs-lookup"><span data-stu-id="28f8d-201">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="28f8d-202">При использовании базовых цветовых схем Visual Studio "Синяя" или "Светлая" шрифт будет пурпурным с полужирным начертанием.</span><span class="sxs-lookup"><span data-stu-id="28f8d-202">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="28f8d-203">Если используется тема "Темная", шрифт будет сине-зеленым с полужирным начертанием.</span><span class="sxs-lookup"><span data-stu-id="28f8d-203">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="28f8d-204">Изображения в этом документе были созданы с помощью темы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="28f8d-204">The images in this document were taken using the default theme.</span></span>

![Изображение](intro/_static/labelaspfor2.png)

<span data-ttu-id="28f8d-206">Вы можете использовать сочетание клавиш Visual Studio *CompleteWord* (CTRL+пробел [по умолчанию](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) внутри двойных кавычек (""), чтобы переключиться на C# так же, как если бы вы находились в классе C#.</span><span class="sxs-lookup"><span data-stu-id="28f8d-206">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="28f8d-207">IntelliSense отобразит все методы и свойства на модели страницы.</span><span class="sxs-lookup"><span data-stu-id="28f8d-207">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="28f8d-208">Методы и свойства доступны, поскольку типом свойства является `ModelExpression`.</span><span class="sxs-lookup"><span data-stu-id="28f8d-208">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="28f8d-209">На рисунке ниже выполняется редактирование представления `Register`, поэтому доступен `RegisterViewModel`.</span><span class="sxs-lookup"><span data-stu-id="28f8d-209">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![Изображение](intro/_static/intellemail.png)

<span data-ttu-id="28f8d-211">IntelliSense выводит свойства и методы, доступные для модели на странице.</span><span class="sxs-lookup"><span data-stu-id="28f8d-211">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="28f8d-212">Благодаря широким возможностям IntelliSense помогает выбрать класс CSS:</span><span class="sxs-lookup"><span data-stu-id="28f8d-212">The rich IntelliSense environment helps you select the CSS class:</span></span>

![Изображение](intro/_static/iclass.png)

![Изображение](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="28f8d-215">Сравнение вспомогательных функций тегов со вспомогательными методами HTML</span><span class="sxs-lookup"><span data-stu-id="28f8d-215">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="28f8d-216">Вспомогательные функции тегов присоединяются к HTML-элементам в :::no-loc(Razor)::: представлениях, а [вспомогательные методы HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) вызываются в виде методов, смешанных с HTML в :::no-loc(Razor)::: представлениях.</span><span class="sxs-lookup"><span data-stu-id="28f8d-216">Tag Helpers attach to HTML elements in :::no-loc(Razor)::: views, while [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in :::no-loc(Razor)::: views.</span></span> <span data-ttu-id="28f8d-217">Рассмотрим следующую :::no-loc(Razor)::: разметку, которая создает HTML-метку с помощью класса CSS "Caption":</span><span class="sxs-lookup"><span data-stu-id="28f8d-217">Consider the following :::no-loc(Razor)::: markup, which creates an HTML label with the CSS class "caption":</span></span>

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="28f8d-218">Символ at () указывает на то, что `@` :::no-loc(Razor)::: это начало кода.</span><span class="sxs-lookup"><span data-stu-id="28f8d-218">The at (`@`) symbol tells :::no-loc(Razor)::: this is the start of code.</span></span> <span data-ttu-id="28f8d-219">Следующие два параметра ("FirstName" и "First Name:") представляют собой строки, поэтому [IntelliSense](/visualstudio/ide/using-intellisense) помочь здесь не может.</span><span class="sxs-lookup"><span data-stu-id="28f8d-219">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="28f8d-220">Последний аргумент:</span><span class="sxs-lookup"><span data-stu-id="28f8d-220">The last argument:</span></span>

```cshtml
new {@class="caption"}
```

<span data-ttu-id="28f8d-221">является анонимным объектом, который используется для представления атрибутов.</span><span class="sxs-lookup"><span data-stu-id="28f8d-221">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="28f8d-222">Так как `class` — это зарезервированное ключевое слово в C#, используется символ `@`, чтобы в C# интерпретировать `@class=` как символ (имя свойства).</span><span class="sxs-lookup"><span data-stu-id="28f8d-222">Because `class` is a reserved keyword in C#, you use the `@` symbol to force C# to interpret `@class=` as a symbol (property name).</span></span> <span data-ttu-id="28f8d-223">В интерфейсном конструкторе (кто знаком с HTML/CSS/JavaScript и другими клиентскими технологиями, но не знаком с C# и :::no-loc(Razor)::: ), большая часть этой строки является внешней.</span><span class="sxs-lookup"><span data-stu-id="28f8d-223">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and :::no-loc(Razor):::), most of the line is foreign.</span></span> <span data-ttu-id="28f8d-224">И вся строка должны быть написана без помощи IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="28f8d-224">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="28f8d-225">С помощью функции `LabelTagHelper` та же разметка может быть написана следующим образом:</span><span class="sxs-lookup"><span data-stu-id="28f8d-225">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

<span data-ttu-id="28f8d-226">При использовании вспомогательной функции тега, как только вы вводите `<l` в редакторе Visual Studio, IntelliSense отобразит подходящие элементы:</span><span class="sxs-lookup"><span data-stu-id="28f8d-226">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![Изображение](intro/_static/label.png)

<span data-ttu-id="28f8d-228">IntelliSense помогает написать всю строку.</span><span class="sxs-lookup"><span data-stu-id="28f8d-228">IntelliSense helps you write the entire line.</span></span>

<span data-ttu-id="28f8d-229">На следующем рисунке кода показана форма *представления/учетной записи/Register. cshtml* , :::no-loc(Razor)::: созданная на основе шаблона MVC ASP.NET 4.5. x, входящего в состав Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="28f8d-229">The following code image shows the Form portion of the *Views/Account/Register.cshtml* :::no-loc(Razor)::: view generated from the ASP.NET 4.5.x MVC template included with Visual Studio.</span></span>

![Изображение](intro/_static/regCS.png)

<span data-ttu-id="28f8d-231">Редактор Visual Studio отображает код C# на сером фоне.</span><span class="sxs-lookup"><span data-stu-id="28f8d-231">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="28f8d-232">Например, вспомогательный метод HTML `AntiForgeryToken`:</span><span class="sxs-lookup"><span data-stu-id="28f8d-232">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```cshtml
@Html.AntiForgeryToken()
```

<span data-ttu-id="28f8d-233">отображается на сером фоне.</span><span class="sxs-lookup"><span data-stu-id="28f8d-233">is displayed with a grey background.</span></span> <span data-ttu-id="28f8d-234">Большая часть разметки в представлении Register является кодом C#.</span><span class="sxs-lookup"><span data-stu-id="28f8d-234">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="28f8d-235">Сравните это с эквивалентным подходом, где используются вспомогательные функции тегов:</span><span class="sxs-lookup"><span data-stu-id="28f8d-235">Compare that to the equivalent approach using Tag Helpers:</span></span>

![Изображение](intro/_static/regTH.png)

<span data-ttu-id="28f8d-237">Разметка здесь более чистая, ее проще читать, редактировать и поддерживать, чем при использовании вспомогательных методов HTML.</span><span class="sxs-lookup"><span data-stu-id="28f8d-237">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="28f8d-238">Количество C# кода уменьшено до минимума — здесь есть только то, что необходимо знать серверу.</span><span class="sxs-lookup"><span data-stu-id="28f8d-238">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="28f8d-239">Редактор Visual Studio отображает разметку, с которой работает вспомогательная функция тега, особым шрифтом.</span><span class="sxs-lookup"><span data-stu-id="28f8d-239">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="28f8d-240">Рассмотрим группу *Email* :</span><span class="sxs-lookup"><span data-stu-id="28f8d-240">Consider the *Email* group:</span></span>

[!code-cshtml[](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="28f8d-241">У каждого из атрибутов "asp-" есть значение "Email", но "Email" не является строкой.</span><span class="sxs-lookup"><span data-stu-id="28f8d-241">Each of the "asp-" attributes has a value of "Email", but "Email" isn't a string.</span></span> <span data-ttu-id="28f8d-242">В данном контексте "Email" — это свойство модельного выражения C# для `RegisterViewModel`.</span><span class="sxs-lookup"><span data-stu-id="28f8d-242">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="28f8d-243">Редактор Visual Studio помогает написать **всю** разметку, если вы используете вспомогательную функцию тега, а если вы применяете вспомогательные методы HTML, помощи Visual Studio для большей части кода можно не ожидать.</span><span class="sxs-lookup"><span data-stu-id="28f8d-243">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="28f8d-244">Дополнительные сведения о работе со вспомогательными функциями тегов в редакторе Visual Studio см. в статье [Поддержка IntelliSense для вспомогательных функций тегов](#intellisense-support-for-tag-helpers).</span><span class="sxs-lookup"><span data-stu-id="28f8d-244">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="28f8d-245">Сравнение вспомогательных функций тегов с серверными веб-элементами управления</span><span class="sxs-lookup"><span data-stu-id="28f8d-245">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="28f8d-246">Вспомогательным функциям тегов не принадлежит элемент, с которыми они связаны. Они просто участвуют в отрисовке элемента и содержимого.</span><span class="sxs-lookup"><span data-stu-id="28f8d-246">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="28f8d-247">ASP.NET <https://docs.microsoft.com/previous-versions/dotnet/netframework-3.0/7698y1f0(v=vs.85)> объявляются и вызываются на странице.</span><span class="sxs-lookup"><span data-stu-id="28f8d-247">ASP.NET <https://docs.microsoft.com/previous-versions/dotnet/netframework-3.0/7698y1f0(v=vs.85)> are declared and invoked on a page.</span></span>

* <span data-ttu-id="28f8d-248"><https://docs.microsoft.com/previous-versions/zsyt68f1(v=vs.140)> наличие нетривиальных жизненных циклов, которые могут усложнить разработку и отладку.</span><span class="sxs-lookup"><span data-stu-id="28f8d-248"><https://docs.microsoft.com/previous-versions/zsyt68f1(v=vs.140)> have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="28f8d-249">Серверные веб-элементы управления позволяют добавлять функционал в элементы DOM с помощью клиентского элемента управления.</span><span class="sxs-lookup"><span data-stu-id="28f8d-249">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="28f8d-250">У вспомогательных функций тегов отсутствует DOM.</span><span class="sxs-lookup"><span data-stu-id="28f8d-250">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="28f8d-251">Серверные веб-элементы управления включают в себя автоматическое обнаружение браузера.</span><span class="sxs-lookup"><span data-stu-id="28f8d-251">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="28f8d-252">Вспомогательные функции тегов ничего не знают о браузере.</span><span class="sxs-lookup"><span data-stu-id="28f8d-252">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="28f8d-253">Несколько вспомогательных функций тегов могут работать с одним и тем же элементом (см. статью о [предотвращении конфликтов вспомогательной функции тега](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts)), серверные веб-элементы управления обычно нельзя комбинировать.</span><span class="sxs-lookup"><span data-stu-id="28f8d-253">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="28f8d-254">Вспомогательные функции тегов могут менять тег и содержимое HTML-элементов, с которыми они связаны, но напрямую они не влияют больше ни на какие элементы на странице.</span><span class="sxs-lookup"><span data-stu-id="28f8d-254">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="28f8d-255">Серверные веб-элементы управления имеют менее конкретную область действия и могут выполнять действия, которые влияют на другие части страницы, и это иногда может привести к побочным эффектам.</span><span class="sxs-lookup"><span data-stu-id="28f8d-255">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="28f8d-256">Серверные веб-элементы управления используют преобразователи типов для преобразования строк в объекты.</span><span class="sxs-lookup"><span data-stu-id="28f8d-256">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="28f8d-257">Со вспомогательными функциями тегов вы работаете изначально в C#, поэтому преобразование типов не требуется.</span><span class="sxs-lookup"><span data-stu-id="28f8d-257">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="28f8d-258">Серверные веб-элементы управления используют [System.ComponentModel](/dotnet/api/system.componentmodel), чтобы реализовать поведение компонентов и элементов управления во время разработки и выполнения.</span><span class="sxs-lookup"><span data-stu-id="28f8d-258">Web Server controls use [System.ComponentModel](/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="28f8d-259">`System.ComponentModel` содержит базовые классы и интерфейсы для реализации атрибутов и преобразователей типов, привязки к источникам данных и лицензирования компонентов.</span><span class="sxs-lookup"><span data-stu-id="28f8d-259">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="28f8d-260">Сравните это со вспомогательными функциями тегов, которые обычно являются производными от `TagHelper`, а базовый класс `TagHelper` предоставляет только два метода — `Process` и `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="28f8d-260">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="28f8d-261">Настройка шрифта элемента вспомогательной функции тега</span><span class="sxs-lookup"><span data-stu-id="28f8d-261">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="28f8d-262">Можно настроить шрифт и цветовую раскраску с помощью **меню Сервис**  >  **Параметры**  >  **Среда**  >  **шрифты и цвета** :</span><span class="sxs-lookup"><span data-stu-id="28f8d-262">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors** :</span></span>

![Изображение](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a><span data-ttu-id="28f8d-264">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="28f8d-264">Additional resources</span></span>

* [<span data-ttu-id="28f8d-265">Создание вспомогательных функций тегов</span><span class="sxs-lookup"><span data-stu-id="28f8d-265">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="28f8d-266">Работа с формами</span><span class="sxs-lookup"><span data-stu-id="28f8d-266">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* <span data-ttu-id="28f8d-267">[TagHelperSamples на GitHub](https://github.com/dpaquette/TagHelperSamples) содержит примеры вспомогательной функции тега для работы с [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="28f8d-267">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](https://getbootstrap.com/).</span></span>
