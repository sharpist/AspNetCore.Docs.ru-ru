---
title: Справочник по синтаксису Razor для ASP.NET Core
author: rick-anderson
description: Сведения о Razor синтаксисе разметки для внедрения серверного кода на веб-страницы.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: 3e77b25e2660688d0040d47840e47dab8f260197
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003193"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="f1a59-103">Справочник по синтаксису Razor для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f1a59-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="f1a59-104">По [Рик Андерсон (](https://twitter.com/RickAndMSFT), [Кузнецова Маллен](https://twitter.com/ntaylormullen)и " [викарел](https://github.com/Rabadash8820) "</span><span class="sxs-lookup"><span data-stu-id="f1a59-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="f1a59-105">Razor — это синтаксис разметки для внедрения в веб-страницы серверного кода.</span><span class="sxs-lookup"><span data-stu-id="f1a59-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="f1a59-106">Синтаксис Razor состоит из разметки Razor, C# и HTML.</span><span class="sxs-lookup"><span data-stu-id="f1a59-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="f1a59-107">Файлы, содержащие Razor, обычно имеют расширение *CSHTML*.</span><span class="sxs-lookup"><span data-stu-id="f1a59-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="f1a59-108">Razor также находится в файлах [компонентов Razor](xref:blazor/components) (*.razor*).</span><span class="sxs-lookup"><span data-stu-id="f1a59-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="f1a59-109">Отрисовка HTML</span><span class="sxs-lookup"><span data-stu-id="f1a59-109">Rendering HTML</span></span>

<span data-ttu-id="f1a59-110">Языком Razor по умолчанию является HTML.</span><span class="sxs-lookup"><span data-stu-id="f1a59-110">The default Razor language is HTML.</span></span> <span data-ttu-id="f1a59-111">Отрисовка HTML из разметки Razor ничем не отличается от отрисовки из HTML-файла.</span><span class="sxs-lookup"><span data-stu-id="f1a59-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="f1a59-112">Сервер отображает разметку HTML в *CSHTML*-файлах Razor без изменений.</span><span class="sxs-lookup"><span data-stu-id="f1a59-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="f1a59-113">Синтаксис Razor</span><span class="sxs-lookup"><span data-stu-id="f1a59-113">Razor syntax</span></span>

<span data-ttu-id="f1a59-114">Razor поддерживает C# и использует символ `@` для перехода с HTML на C#.</span><span class="sxs-lookup"><span data-stu-id="f1a59-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="f1a59-115">Razor вычисляет выражения C# и отображает их в выходных данных HTML.</span><span class="sxs-lookup"><span data-stu-id="f1a59-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="f1a59-116">Если за символом `@` следует [зарезервированное ключевое слово Razor](#razor-reserved-keywords), он переходит на разметку Razor.</span><span class="sxs-lookup"><span data-stu-id="f1a59-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="f1a59-117">В противном случае он переходит на обычный C#.</span><span class="sxs-lookup"><span data-stu-id="f1a59-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="f1a59-118">В качестве escape-символа для `@` в разметке Razor используйте второй символ `@`:</span><span class="sxs-lookup"><span data-stu-id="f1a59-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="f1a59-119">Код будет отображен в HTML с одним символом `@`:</span><span class="sxs-lookup"><span data-stu-id="f1a59-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="f1a59-120">HTML-атрибуты и содержимое, включающие адреса электронной почты, не расценивают символ `@` как символ перехода.</span><span class="sxs-lookup"><span data-stu-id="f1a59-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="f1a59-121">В следующем примере синтаксический анализ Razor не затрагивает адреса электронной почты:</span><span class="sxs-lookup"><span data-stu-id="f1a59-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="f1a59-122">Неявные выражения Razor</span><span class="sxs-lookup"><span data-stu-id="f1a59-122">Implicit Razor expressions</span></span>

<span data-ttu-id="f1a59-123">Неявные выражения Razor начинаются с символа `@`, за которым следует код C#:</span><span class="sxs-lookup"><span data-stu-id="f1a59-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="f1a59-124">Неявные выражения не должны содержать пробелов. Исключением является ключевое слово C# `await`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="f1a59-125">Если оператор C# имеет четкое окончание, пробелы вставлять можно:</span><span class="sxs-lookup"><span data-stu-id="f1a59-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="f1a59-126">Неявные выражения **не могут** содержать универсальные шаблоны C#, так как символы в угловых скобках (`<>`) интерпретируются как тег HTML.</span><span class="sxs-lookup"><span data-stu-id="f1a59-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="f1a59-127">Следующий код является **недопустимым**:</span><span class="sxs-lookup"><span data-stu-id="f1a59-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="f1a59-128">Приведенный выше код вызывает ошибку компилятора примерно следующего вида:</span><span class="sxs-lookup"><span data-stu-id="f1a59-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="f1a59-129">Элемент "int" не был закрыт.</span><span class="sxs-lookup"><span data-stu-id="f1a59-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="f1a59-130">Все элементы должны быть самозакрывающимися или иметь соответствующий закрывающий тег.</span><span class="sxs-lookup"><span data-stu-id="f1a59-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="f1a59-131">Не удается преобразовать группу методов "GenericMethod" в не являющийся делегатом тип "object".</span><span class="sxs-lookup"><span data-stu-id="f1a59-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="f1a59-132">Предполагалось вызывать этот метод?</span><span class="sxs-lookup"><span data-stu-id="f1a59-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="f1a59-133">Вызовы универсальных методов должны быть заключены в [явное выражение Razor](#explicit-razor-expressions) или [блок кода Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="f1a59-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="f1a59-134">Явные выражения Razor</span><span class="sxs-lookup"><span data-stu-id="f1a59-134">Explicit Razor expressions</span></span>

<span data-ttu-id="f1a59-135">Явные выражения Razor состоят из символа `@` с соответствующими открывающими и закрывающими скобками.</span><span class="sxs-lookup"><span data-stu-id="f1a59-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="f1a59-136">Для отображения времени прошлой недели используется следующая разметка Razor:</span><span class="sxs-lookup"><span data-stu-id="f1a59-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="f1a59-137">Любое содержимое в скобках `@()` вычисляется и отображается в выходных данных.</span><span class="sxs-lookup"><span data-stu-id="f1a59-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="f1a59-138">Неявные выражения, описанные в предыдущем разделе, обычно не содержат пробелов.</span><span class="sxs-lookup"><span data-stu-id="f1a59-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="f1a59-139">В следующем коде из значения текущего времени неделя не вычитается:</span><span class="sxs-lookup"><span data-stu-id="f1a59-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="f1a59-140">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="f1a59-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="f1a59-141">Явные выражения позволяют объединять результат своего выполнения с дополнительным текстом:</span><span class="sxs-lookup"><span data-stu-id="f1a59-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="f1a59-142">Без явного выражения `<p>Age@joe.Age</p>` обрабатывается как адрес электронной почты, и на выходе отображается `<p>Age@joe.Age</p>`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="f1a59-143">Если же текст написан как явное выражение, то вы получите `<p>Age33</p>`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="f1a59-144">Вы можете использовать явные выражения для отображения выходных данных из универсальных методов в файлах *CSHTML*.</span><span class="sxs-lookup"><span data-stu-id="f1a59-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="f1a59-145">В следующем примере показано, как исправить ошибку, показанную ранее и вызванную скобками в универсальном шаблоне C#.</span><span class="sxs-lookup"><span data-stu-id="f1a59-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="f1a59-146">Код записывается как явное выражение:</span><span class="sxs-lookup"><span data-stu-id="f1a59-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="f1a59-147">Кодирование выражений</span><span class="sxs-lookup"><span data-stu-id="f1a59-147">Expression encoding</span></span>

<span data-ttu-id="f1a59-148">Выражения C#, которые имеют строковое выходное значение, кодируются в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="f1a59-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="f1a59-149">Выражения C#, которые имеют значение `IHtmlContent`, обрабатываются непосредственно с помощью `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="f1a59-150">Выражения C#, которые не имеют выходное значение `IHtmlContent`, преобразуются в строку с помощью `ToString` и кодируются перед обработкой.</span><span class="sxs-lookup"><span data-stu-id="f1a59-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="f1a59-151">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="f1a59-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="f1a59-152">HTML отображается в обозревателе следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f1a59-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="f1a59-153">Выходные данные `HtmlHelper.Raw` не кодируются, но отображаются в виде разметки HTML.</span><span class="sxs-lookup"><span data-stu-id="f1a59-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="f1a59-154">Использование `HtmlHelper.Raw` с непроверенными входными данными пользователя представляет угрозу безопасности.</span><span class="sxs-lookup"><span data-stu-id="f1a59-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="f1a59-155">Эти входные данные могут содержать вредоносный код JavaScript или другие эксплойты.</span><span class="sxs-lookup"><span data-stu-id="f1a59-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="f1a59-156">Очистка вводимых пользователем данных является сложной задачей.</span><span class="sxs-lookup"><span data-stu-id="f1a59-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="f1a59-157">Старайтесь не использовать `HtmlHelper.Raw` с такими данными.</span><span class="sxs-lookup"><span data-stu-id="f1a59-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="f1a59-158">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="f1a59-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="f1a59-159">Блоки кода Razor</span><span class="sxs-lookup"><span data-stu-id="f1a59-159">Razor code blocks</span></span>

<span data-ttu-id="f1a59-160">Блоки кода Razor начинаются с символа `@` и заключены в фигурные скобки `{}`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="f1a59-161">В отличие от выражений код C# внутри блоков кода не обрабатывается.</span><span class="sxs-lookup"><span data-stu-id="f1a59-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="f1a59-162">Блоки кода и выражения в представлении используют общую область и определяются в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="f1a59-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="f1a59-163">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="f1a59-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1a59-164">В блоках кода объявите [локальные функции](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) с помощью разметки для использования в качестве методов создания шаблонов:</span><span class="sxs-lookup"><span data-stu-id="f1a59-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="f1a59-165">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="f1a59-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="f1a59-166">Неявные переходы</span><span class="sxs-lookup"><span data-stu-id="f1a59-166">Implicit transitions</span></span>

<span data-ttu-id="f1a59-167">В блоке кода языком по умолчанию является C#, но страница Razor Page может вернуться на HTML:</span><span class="sxs-lookup"><span data-stu-id="f1a59-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="f1a59-168">Явный переход с разделителями</span><span class="sxs-lookup"><span data-stu-id="f1a59-168">Explicit delimited transition</span></span>

<span data-ttu-id="f1a59-169">Чтобы определить подраздел блока кода, который должен отрисовывать HTML, окружите подлежащие отображению символы тегами Razor `<text>`:</span><span class="sxs-lookup"><span data-stu-id="f1a59-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="f1a59-170">Используйте этот способ для отрисовки HTML, не заключенного в HTML-теги.</span><span class="sxs-lookup"><span data-stu-id="f1a59-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="f1a59-171">Без тега HTML или Razor возникает ошибка времени выполнения Razor.</span><span class="sxs-lookup"><span data-stu-id="f1a59-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="f1a59-172">Тег `<text>` хорошо подходит для контроля пробелов при отрисовке содержимого:</span><span class="sxs-lookup"><span data-stu-id="f1a59-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="f1a59-173">Отрисовывается только содержимое между тегами `<text>`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="f1a59-174">В выходных данных HTML пробелы до или после тега `<text>` не отображаются.</span><span class="sxs-lookup"><span data-stu-id="f1a59-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="f1a59-175">Явный перенос строки</span><span class="sxs-lookup"><span data-stu-id="f1a59-175">Explicit line transition</span></span>

<span data-ttu-id="f1a59-176">Для отрисовки оставшейся части строки в виде HTML внутри блока кода используйте синтаксис `@:`:</span><span class="sxs-lookup"><span data-stu-id="f1a59-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="f1a59-177">Если в коде отсутствует `@:`, возникает ошибка среды выполнения Razor.</span><span class="sxs-lookup"><span data-stu-id="f1a59-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="f1a59-178">Дополнительные символы `@` в файле Razor могут вызвать ошибки компилятора в последующих операторах блока.</span><span class="sxs-lookup"><span data-stu-id="f1a59-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="f1a59-179">Эти ошибки компилятора может быть трудно проанализировать, так как ошибка фактически возникает раньше, чем указано.</span><span class="sxs-lookup"><span data-stu-id="f1a59-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="f1a59-180">Чаще всего эта ошибка появляется после объединения множества неявных или явных выражений в один блок кода.</span><span class="sxs-lookup"><span data-stu-id="f1a59-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="f1a59-181">Управляющие структуры</span><span class="sxs-lookup"><span data-stu-id="f1a59-181">Control structures</span></span>

<span data-ttu-id="f1a59-182">Управляющие структуры являются расширением блоков кода.</span><span class="sxs-lookup"><span data-stu-id="f1a59-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="f1a59-183">Все аспекты блоков кода (переход на разметку, встроенный код C#) также относятся к следующим структурам.</span><span class="sxs-lookup"><span data-stu-id="f1a59-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="f1a59-184">Условные выражения \@if, else if, else и \@switch</span><span class="sxs-lookup"><span data-stu-id="f1a59-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="f1a59-185">`@if` контролирует, когда нужно запускать код:</span><span class="sxs-lookup"><span data-stu-id="f1a59-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="f1a59-186">Для `else` и `else if` символ `@` не требуется:</span><span class="sxs-lookup"><span data-stu-id="f1a59-186">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="f1a59-187">В следующей разметке показано использование оператора switch:</span><span class="sxs-lookup"><span data-stu-id="f1a59-187">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="f1a59-188">Циклы \@for, \@foreach, \@while и \@do while</span><span class="sxs-lookup"><span data-stu-id="f1a59-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="f1a59-189">Операторы выполнения цикла позволяют выполнять отрисовку шаблонного HTML.</span><span class="sxs-lookup"><span data-stu-id="f1a59-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="f1a59-190">Отрисовка списка людей:</span><span class="sxs-lookup"><span data-stu-id="f1a59-190">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="f1a59-191">Поддерживаются следующие операторы выполнения цикла:</span><span class="sxs-lookup"><span data-stu-id="f1a59-191">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="f1a59-192">Составные \@using</span><span class="sxs-lookup"><span data-stu-id="f1a59-192">Compound \@using</span></span>

<span data-ttu-id="f1a59-193">В C# оператор `using` позволяет обеспечить использование какого-то объекта.</span><span class="sxs-lookup"><span data-stu-id="f1a59-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="f1a59-194">В Razor этот механизм позволяет создавать вспомогательные функции HTML, включающие дополнительное содержимое.</span><span class="sxs-lookup"><span data-stu-id="f1a59-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="f1a59-195">В следующем коде вспомогательные функции HTML используют оператор `@using` для создания тега `<form>`:</span><span class="sxs-lookup"><span data-stu-id="f1a59-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="f1a59-196">\@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="f1a59-196">\@try, catch, finally</span></span>

<span data-ttu-id="f1a59-197">Обработка исключений выполняется так же, как в C#:</span><span class="sxs-lookup"><span data-stu-id="f1a59-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="f1a59-198">\@lock</span><span class="sxs-lookup"><span data-stu-id="f1a59-198">\@lock</span></span>

<span data-ttu-id="f1a59-199">Razor позволяет защищать важные разделы при помощи операторов блокировки:</span><span class="sxs-lookup"><span data-stu-id="f1a59-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="f1a59-200">Комментарии</span><span class="sxs-lookup"><span data-stu-id="f1a59-200">Comments</span></span>

<span data-ttu-id="f1a59-201">Razor поддерживает комментарии C# и HTML:</span><span class="sxs-lookup"><span data-stu-id="f1a59-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="f1a59-202">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="f1a59-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="f1a59-203">Сервер удаляет комментарии Razor перед отображением веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="f1a59-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="f1a59-204">Для разделения комментариев Razor использует `@*  *@`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="f1a59-205">Следующий код закомментирован, поэтому сервер не отрисовывает разметку:</span><span class="sxs-lookup"><span data-stu-id="f1a59-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="f1a59-206">Директивы</span><span class="sxs-lookup"><span data-stu-id="f1a59-206">Directives</span></span>

<span data-ttu-id="f1a59-207">Директивы Razor представлены неявными выражениями с зарезервированными ключевыми словами после символа `@`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="f1a59-208">Как правило, директива изменяет способ анализа представления или открывает доступ к дополнительным функциям.</span><span class="sxs-lookup"><span data-stu-id="f1a59-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="f1a59-209">Узнав, каким образом Razor создает код для представления, вы сможете легко понять принципы работы директив.</span><span class="sxs-lookup"><span data-stu-id="f1a59-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="f1a59-210">Код создает класс, аналогичный следующему:</span><span class="sxs-lookup"><span data-stu-id="f1a59-210">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="f1a59-211">Сведения о просмотре этого класса приводятся в разделе [Просмотр Razor-класса C#, созданного для представления](#inspect-the-razor-c-class-generated-for-a-view) далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="f1a59-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="f1a59-212">\@attribute</span><span class="sxs-lookup"><span data-stu-id="f1a59-212">\@attribute</span></span>

<span data-ttu-id="f1a59-213">Директива `@attribute` добавляет данный атрибут к классу созданной страницы или представления.</span><span class="sxs-lookup"><span data-stu-id="f1a59-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="f1a59-214">В следующем примере добавляется атрибут `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="f1a59-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="f1a59-215">\@code</span><span class="sxs-lookup"><span data-stu-id="f1a59-215">\@code</span></span>

<span data-ttu-id="f1a59-216">*Этот сценарий применяется только к компонентам Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f1a59-217">Блок `@code` позволяет [компоненту Razor](xref:blazor/components) добавлять в компонент элементы C# (поля, свойства и методы):</span><span class="sxs-lookup"><span data-stu-id="f1a59-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="f1a59-218">Для компонентов Razor `@code` является псевдонимом [`@functions`](#functions) и рекомендуется для. `@functions`</span><span class="sxs-lookup"><span data-stu-id="f1a59-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="f1a59-219">Допускается более одного блока `@code`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="f1a59-220">\@functions</span><span class="sxs-lookup"><span data-stu-id="f1a59-220">\@functions</span></span>

<span data-ttu-id="f1a59-221">Директива `@functions` позволяет добавлять элементы C# (поля, свойства и методы) в создаваемый класс:</span><span class="sxs-lookup"><span data-stu-id="f1a59-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1a59-222">В [компонентах Razor](xref:blazor/components) используйте `@code`для добавления членов C# вместо `@functions`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="f1a59-223">Пример:</span><span class="sxs-lookup"><span data-stu-id="f1a59-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="f1a59-224">Код создает следующую разметку HTML:</span><span class="sxs-lookup"><span data-stu-id="f1a59-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="f1a59-225">Следующий код показывает созданный Razor-класс C#:</span><span class="sxs-lookup"><span data-stu-id="f1a59-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1a59-226">Методы `@functions` служат в качестве методов создания шаблонов при наличии разметки:</span><span class="sxs-lookup"><span data-stu-id="f1a59-226">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="f1a59-227">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="f1a59-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="f1a59-228">\@implements</span><span class="sxs-lookup"><span data-stu-id="f1a59-228">\@implements</span></span>

<span data-ttu-id="f1a59-229">Директива `@implements` реализует интерфейс для созданного класса.</span><span class="sxs-lookup"><span data-stu-id="f1a59-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="f1a59-230">В следующем примере реализуется <xref:System.IDisposable?displayProperty=fullName>, чтобы можно было вызывать метод <xref:System.IDisposable.Dispose*>:</span><span class="sxs-lookup"><span data-stu-id="f1a59-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### <a name="inherits"></a><span data-ttu-id="f1a59-231">\@inherits</span><span class="sxs-lookup"><span data-stu-id="f1a59-231">\@inherits</span></span>

<span data-ttu-id="f1a59-232">Директива `@inherits` позволяет полностью управлять классом, которому наследует представление:</span><span class="sxs-lookup"><span data-stu-id="f1a59-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="f1a59-233">Следующий код показывает настраиваемый тип страницы Razor:</span><span class="sxs-lookup"><span data-stu-id="f1a59-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="f1a59-234">В представлении отображается `CustomText`:</span><span class="sxs-lookup"><span data-stu-id="f1a59-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="f1a59-235">Код отображает следующий HTML:</span><span class="sxs-lookup"><span data-stu-id="f1a59-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="f1a59-236">`@model` и `@inherits` могут использоваться в одном представлении.</span><span class="sxs-lookup"><span data-stu-id="f1a59-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="f1a59-237">`@inherits` может находиться в файле *_ViewImports.cshtml*, который импортируется представлением:</span><span class="sxs-lookup"><span data-stu-id="f1a59-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="f1a59-238">Следующий код показывает пример строго типизированного представления:</span><span class="sxs-lookup"><span data-stu-id="f1a59-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="f1a59-239">Если передать в модель "rick@contoso.com", представление создает следующую разметку HTML:</span><span class="sxs-lookup"><span data-stu-id="f1a59-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="f1a59-240">\@inject</span><span class="sxs-lookup"><span data-stu-id="f1a59-240">\@inject</span></span>

<span data-ttu-id="f1a59-241">Директива `@inject` позволяет странице Razor внедрять в представление службу из [контейнера службы](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f1a59-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="f1a59-242">Дополнительные сведения: [Внедрение зависимостей в представления](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f1a59-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="f1a59-243">\@layout</span><span class="sxs-lookup"><span data-stu-id="f1a59-243">\@layout</span></span>

<span data-ttu-id="f1a59-244">*Этот сценарий применяется только к компонентам Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f1a59-245">Директива `@layout` задает макет для компонента Razor.</span><span class="sxs-lookup"><span data-stu-id="f1a59-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="f1a59-246">Компоненты макета используются, чтобы избежать дублирования и несогласованности кода.</span><span class="sxs-lookup"><span data-stu-id="f1a59-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="f1a59-247">Для получения дополнительной информации см. <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="f1a59-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="f1a59-248">\@model</span><span class="sxs-lookup"><span data-stu-id="f1a59-248">\@model</span></span>

<span data-ttu-id="f1a59-249">*Этот сценарий применяется только к представлениям MVC и Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="f1a59-250">Директива `@model` определяет тип модели, передаваемой в представление или страницу:</span><span class="sxs-lookup"><span data-stu-id="f1a59-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="f1a59-251">В MVC-приложении ASP.NET Core или Razor Pages, созданном с отдельными учетными записями пользователей, представление *Views/Account/Login.cshtml* содержит следующее объявление модели:</span><span class="sxs-lookup"><span data-stu-id="f1a59-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="f1a59-252">Созданный класс наследует от `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="f1a59-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="f1a59-253">Для доступа к модели, переданной в представление, Razor предоставляет свойство `Model`:</span><span class="sxs-lookup"><span data-stu-id="f1a59-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="f1a59-254">Директива `@model` задает тип свойства `Model`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="f1a59-255">Директива указывает `T` в `RazorPage<T>` — созданном классе, на основе которого создается производное представление.</span><span class="sxs-lookup"><span data-stu-id="f1a59-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="f1a59-256">Если директива `@model` не указана, свойство `Model` имеет тип `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="f1a59-257">Дополнительные сведения см. [в @model разделе модели со строгой типизацией и ключевое слово](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="f1a59-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="f1a59-258">\@namespace</span><span class="sxs-lookup"><span data-stu-id="f1a59-258">\@namespace</span></span>

<span data-ttu-id="f1a59-259">Директива `@namespace`:</span><span class="sxs-lookup"><span data-stu-id="f1a59-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="f1a59-260">задает пространство имен класса созданной страницы Razor, представления MVC или компонента Razor.</span><span class="sxs-lookup"><span data-stu-id="f1a59-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="f1a59-261">Устанавливает корневые производные пространства имен страниц, представлений или классов компонентов из ближайшего файла импорта в дереве каталогов, *_ViewImports.cshtml* (представления или страницы) или *_Imports.razor* (компоненты Razor).</span><span class="sxs-lookup"><span data-stu-id="f1a59-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="f1a59-262">Для примера Razor Pages, приведенного в следующей таблице:</span><span class="sxs-lookup"><span data-stu-id="f1a59-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="f1a59-263">Каждая страница импортирует *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f1a59-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="f1a59-264">Файл *Pages/_ViewImports.cshtml* содержит `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="f1a59-265">Каждая страница имеет `Hello.World` в качестве корня пространства имен.</span><span class="sxs-lookup"><span data-stu-id="f1a59-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="f1a59-266">Страница</span><span class="sxs-lookup"><span data-stu-id="f1a59-266">Page</span></span>                                        | <span data-ttu-id="f1a59-267">Пространство имен</span><span class="sxs-lookup"><span data-stu-id="f1a59-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="f1a59-268">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a59-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="f1a59-269">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a59-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="f1a59-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a59-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="f1a59-271">Описанные отношения применяются к файлам импорта, которые используются в представлениях MVC и компонентах Razor.</span><span class="sxs-lookup"><span data-stu-id="f1a59-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="f1a59-272">Если у нескольких файлов импорта есть директива `@namespace`, для задания корневого пространства имен используется файл, ближайший к странице, компоненту или представлению в дереве каталогов.</span><span class="sxs-lookup"><span data-stu-id="f1a59-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="f1a59-273">Если папка *EvenMorePages* в предыдущем примере содержит файл импорта с `@namespace Another.Planet` (или же файл *Pages/MorePages/EvenMorePages/Page.cshtml* содержит `@namespace Another.Planet`), результат отображается в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="f1a59-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="f1a59-274">Страница</span><span class="sxs-lookup"><span data-stu-id="f1a59-274">Page</span></span>                                        | <span data-ttu-id="f1a59-275">Пространство имен</span><span class="sxs-lookup"><span data-stu-id="f1a59-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="f1a59-276">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a59-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="f1a59-277">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a59-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="f1a59-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a59-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="f1a59-279">\@page</span><span class="sxs-lookup"><span data-stu-id="f1a59-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1a59-280">Директива `@page` имеет различные эффекты в зависимости от типа файла, в котором она используется.</span><span class="sxs-lookup"><span data-stu-id="f1a59-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="f1a59-281">Директива:</span><span class="sxs-lookup"><span data-stu-id="f1a59-281">The directive:</span></span>

* <span data-ttu-id="f1a59-282">В *.cshtml*-файле указывает, что файл является страницей Razor.</span><span class="sxs-lookup"><span data-stu-id="f1a59-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="f1a59-283">Дополнительные сведения см. в разделе [Пользовательские маршруты](xref:razor-pages/index#custom-routes) и <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="f1a59-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="f1a59-284">Указывает, что компонент Razor должен выполнять запросы напрямую.</span><span class="sxs-lookup"><span data-stu-id="f1a59-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="f1a59-285">Для получения дополнительной информации см. <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="f1a59-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f1a59-286">Директива `@page` в первой строке *.cshtml*-файла указывает, что файл является страницей Razor.</span><span class="sxs-lookup"><span data-stu-id="f1a59-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="f1a59-287">Для получения дополнительной информации см. <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="f1a59-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="f1a59-288">\@section</span><span class="sxs-lookup"><span data-stu-id="f1a59-288">\@section</span></span>

<span data-ttu-id="f1a59-289">*Этот сценарий применяется только к представлениям MVC и Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="f1a59-290">Директива `@section` используется в сочетании с [макетами MVC и Razor Pages](xref:mvc/views/layout) и позволяет представлениям и страницам отображать содержимое в различных частях HTML-страницы.</span><span class="sxs-lookup"><span data-stu-id="f1a59-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="f1a59-291">Для получения дополнительной информации см. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="f1a59-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="f1a59-292">\@using</span><span class="sxs-lookup"><span data-stu-id="f1a59-292">\@using</span></span>

<span data-ttu-id="f1a59-293">Директива `@using` добавляет директиву C# `using` в созданное представление:</span><span class="sxs-lookup"><span data-stu-id="f1a59-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1a59-294">В [компонентах Razor](xref:blazor/components) `@using` также определяет, какие компоненты находятся в области.</span><span class="sxs-lookup"><span data-stu-id="f1a59-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="f1a59-295">Атрибуты директивы</span><span class="sxs-lookup"><span data-stu-id="f1a59-295">Directive attributes</span></span>

### <a name="attributes"></a><span data-ttu-id="f1a59-296">\@attributes</span><span class="sxs-lookup"><span data-stu-id="f1a59-296">\@attributes</span></span>

<span data-ttu-id="f1a59-297">*Этот сценарий применяется только к компонентам Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f1a59-298">`@attributes` позволяет компоненту обрабатывать необъявленные атрибуты.</span><span class="sxs-lookup"><span data-stu-id="f1a59-298">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="f1a59-299">Для получения дополнительной информации см. <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="f1a59-299">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="f1a59-300">\@bind</span><span class="sxs-lookup"><span data-stu-id="f1a59-300">\@bind</span></span>

<span data-ttu-id="f1a59-301">*Этот сценарий применяется только к компонентам Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f1a59-302">Привязка данных в компонентах выполняется с помощью атрибута `@bind`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-302">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="f1a59-303">Для получения дополнительной информации см. <xref:blazor/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="f1a59-303">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="f1a59-304">\@on{СОБЫТИЕ}</span><span class="sxs-lookup"><span data-stu-id="f1a59-304">\@on{EVENT}</span></span>

<span data-ttu-id="f1a59-305">*Этот сценарий применяется только к компонентам Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-305">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f1a59-306">Razor предоставляет функции обработки событий для компонентов.</span><span class="sxs-lookup"><span data-stu-id="f1a59-306">Razor provides event handling features for components.</span></span> <span data-ttu-id="f1a59-307">Для получения дополнительной информации см. <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="f1a59-307">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="f1a59-308">\@on{СОБЫТИЕ}:preventDefault</span><span class="sxs-lookup"><span data-stu-id="f1a59-308">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="f1a59-309">*Этот сценарий применяется только к компонентам Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-309">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f1a59-310">Запрещает выполнение действия по умолчанию для события.</span><span class="sxs-lookup"><span data-stu-id="f1a59-310">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="f1a59-311">\@on{СОБЫТИЕ}:stopPropagation</span><span class="sxs-lookup"><span data-stu-id="f1a59-311">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="f1a59-312">*Этот сценарий применяется только к компонентам Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-312">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f1a59-313">Останавливает распространение события.</span><span class="sxs-lookup"><span data-stu-id="f1a59-313">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="f1a59-314">\@key</span><span class="sxs-lookup"><span data-stu-id="f1a59-314">\@key</span></span>

<span data-ttu-id="f1a59-315">*Этот сценарий применяется только к компонентам Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-315">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f1a59-316">Атрибут директивы `@key` заставляет алгоритм сравнения компонентов гарантировать сохранение элементов или компонентов на основе значения ключа.</span><span class="sxs-lookup"><span data-stu-id="f1a59-316">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="f1a59-317">Для получения дополнительной информации см. <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="f1a59-317">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="f1a59-318">\@ref</span><span class="sxs-lookup"><span data-stu-id="f1a59-318">\@ref</span></span>

<span data-ttu-id="f1a59-319">*Этот сценарий применяется только к компонентам Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-319">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f1a59-320">Ссылки на компоненты (`@ref`) предоставляют способ ссылаться на экземпляр компонента, чтобы можно было выполнять команды для этого экземпляра.</span><span class="sxs-lookup"><span data-stu-id="f1a59-320">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="f1a59-321">Для получения дополнительной информации см. <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="f1a59-321">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="f1a59-322">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="f1a59-322">\@typeparam</span></span>

<span data-ttu-id="f1a59-323">*Этот сценарий применяется только к компонентам Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-323">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f1a59-324">Директива `@typeparam` объявляет параметр универсального типа для созданного класса компонента.</span><span class="sxs-lookup"><span data-stu-id="f1a59-324">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="f1a59-325">Для получения дополнительной информации см. <xref:blazor/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="f1a59-325">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="f1a59-326">Шаблонные делегаты Razor</span><span class="sxs-lookup"><span data-stu-id="f1a59-326">Templated Razor delegates</span></span>

<span data-ttu-id="f1a59-327">Шаблоны Razor позволяют определить фрагмент кода пользовательского интерфейса в следующем формате:</span><span class="sxs-lookup"><span data-stu-id="f1a59-327">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="f1a59-328">Следующий пример показывает, как указать шаблонный делегат Razor в виде <xref:System.Func%602>.</span><span class="sxs-lookup"><span data-stu-id="f1a59-328">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="f1a59-329">[Динамический тип](/dotnet/csharp/programming-guide/types/using-type-dynamic) указывается для параметра метода, инкапсулируемого делегатом.</span><span class="sxs-lookup"><span data-stu-id="f1a59-329">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="f1a59-330">[Тип объекта](/dotnet/csharp/language-reference/keywords/object) указывается в качестве возвращаемого значения делегата.</span><span class="sxs-lookup"><span data-stu-id="f1a59-330">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="f1a59-331">Этот шаблон используется с <xref:System.Collections.Generic.List%601>объекта `Pet`, имеющим свойство `Name`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-331">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="f1a59-332">Шаблон отрисовывается с использованием `pets`, предоставляемого оператором `foreach`:</span><span class="sxs-lookup"><span data-stu-id="f1a59-332">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="f1a59-333">Отображенные выходные данные:</span><span class="sxs-lookup"><span data-stu-id="f1a59-333">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="f1a59-334">Вы также можете предоставить встроенный шаблон Razor в качестве аргумента для метода.</span><span class="sxs-lookup"><span data-stu-id="f1a59-334">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="f1a59-335">В следующем примере метод `Repeat` получает шаблон Razor.</span><span class="sxs-lookup"><span data-stu-id="f1a59-335">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="f1a59-336">Метод использует этот шаблон для создания HTML-содержимого с повторениями элементов из списка:</span><span class="sxs-lookup"><span data-stu-id="f1a59-336">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="f1a59-337">С использованием списка домашних животных из предыдущего примера метод `Repeat` вызывается следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f1a59-337">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="f1a59-338"><xref:System.Collections.Generic.List%601> объекта `Pet`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-338"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="f1a59-339">Количество повторений для каждого домашнего животного.</span><span class="sxs-lookup"><span data-stu-id="f1a59-339">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="f1a59-340">Встроенный шаблон, используемый для перечисления элементов неупорядоченного списка.</span><span class="sxs-lookup"><span data-stu-id="f1a59-340">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="f1a59-341">Отображенные выходные данные:</span><span class="sxs-lookup"><span data-stu-id="f1a59-341">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="f1a59-342">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="f1a59-342">Tag Helpers</span></span>

<span data-ttu-id="f1a59-343">*Этот сценарий применяется только к представлениям MVC и Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="f1a59-343">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="f1a59-344">Существует три директивы, которые относятся к [вспомогательным функциям тегов](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="f1a59-344">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="f1a59-345">Директива</span><span class="sxs-lookup"><span data-stu-id="f1a59-345">Directive</span></span> | <span data-ttu-id="f1a59-346">Функция</span><span class="sxs-lookup"><span data-stu-id="f1a59-346">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="f1a59-347">Делает вспомогательные функции тегов доступными в представлении.</span><span class="sxs-lookup"><span data-stu-id="f1a59-347">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="f1a59-348">Удаляет из представления вспомогательные функции тегов, добавленные ранее.</span><span class="sxs-lookup"><span data-stu-id="f1a59-348">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="f1a59-349">Задает префикс тега, который активирует поддержку вспомогательной функции тега и ее использования в явном виде.</span><span class="sxs-lookup"><span data-stu-id="f1a59-349">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a>Razor<span data-ttu-id="f1a59-350">зарезервированные ключевые слова</span><span class="sxs-lookup"><span data-stu-id="f1a59-350"> reserved keywords</span></span>

### <a name="razor-keywords"></a>Razor<span data-ttu-id="f1a59-351">словами</span><span class="sxs-lookup"><span data-stu-id="f1a59-351"> keywords</span></span>

* <span data-ttu-id="f1a59-352">page (требуется ASP.NET Core 2.1 или более поздней версии)</span><span class="sxs-lookup"><span data-stu-id="f1a59-352">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="f1a59-353">namespace</span><span class="sxs-lookup"><span data-stu-id="f1a59-353">namespace</span></span>
* <span data-ttu-id="f1a59-354">functions</span><span class="sxs-lookup"><span data-stu-id="f1a59-354">functions</span></span>
* <span data-ttu-id="f1a59-355">наследует</span><span class="sxs-lookup"><span data-stu-id="f1a59-355">inherits</span></span>
* <span data-ttu-id="f1a59-356">model</span><span class="sxs-lookup"><span data-stu-id="f1a59-356">model</span></span>
* <span data-ttu-id="f1a59-357">section</span><span class="sxs-lookup"><span data-stu-id="f1a59-357">section</span></span>
* <span data-ttu-id="f1a59-358">helper (сейчас не поддерживается в ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="f1a59-358">helper (Not currently supported by ASP.NET Core)</span></span>

Razor<span data-ttu-id="f1a59-359">Ключевые слова экранированы `@(Razor Keyword)` с помощью (например `@(functions)`,).</span><span class="sxs-lookup"><span data-stu-id="f1a59-359"> keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="f1a59-360">Ключевые Razor слова C#</span><span class="sxs-lookup"><span data-stu-id="f1a59-360">C# Razor keywords</span></span>

* <span data-ttu-id="f1a59-361">case</span><span class="sxs-lookup"><span data-stu-id="f1a59-361">case</span></span>
* <span data-ttu-id="f1a59-362">do</span><span class="sxs-lookup"><span data-stu-id="f1a59-362">do</span></span>
* <span data-ttu-id="f1a59-363">default</span><span class="sxs-lookup"><span data-stu-id="f1a59-363">default</span></span>
* <span data-ttu-id="f1a59-364">for</span><span class="sxs-lookup"><span data-stu-id="f1a59-364">for</span></span>
* <span data-ttu-id="f1a59-365">foreach</span><span class="sxs-lookup"><span data-stu-id="f1a59-365">foreach</span></span>
* <span data-ttu-id="f1a59-366">if</span><span class="sxs-lookup"><span data-stu-id="f1a59-366">if</span></span>
* <span data-ttu-id="f1a59-367">else</span><span class="sxs-lookup"><span data-stu-id="f1a59-367">else</span></span>
* <span data-ttu-id="f1a59-368">lock</span><span class="sxs-lookup"><span data-stu-id="f1a59-368">lock</span></span>
* <span data-ttu-id="f1a59-369">switch</span><span class="sxs-lookup"><span data-stu-id="f1a59-369">switch</span></span>
* <span data-ttu-id="f1a59-370">попробуйте выполнить следующее</span><span class="sxs-lookup"><span data-stu-id="f1a59-370">try</span></span>
* <span data-ttu-id="f1a59-371">catch</span><span class="sxs-lookup"><span data-stu-id="f1a59-371">catch</span></span>
* <span data-ttu-id="f1a59-372">finally</span><span class="sxs-lookup"><span data-stu-id="f1a59-372">finally</span></span>
* <span data-ttu-id="f1a59-373">using</span><span class="sxs-lookup"><span data-stu-id="f1a59-373">using</span></span>
* <span data-ttu-id="f1a59-374">while</span><span class="sxs-lookup"><span data-stu-id="f1a59-374">while</span></span>

<span data-ttu-id="f1a59-375">Ключевые Razor слова C# должны быть двойным образом `@(@C# Razor Keyword)` экранированы с помощью `@(@case)`(например,).</span><span class="sxs-lookup"><span data-stu-id="f1a59-375">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="f1a59-376">Первый `@` заключается в escape- Razor анализе.</span><span class="sxs-lookup"><span data-stu-id="f1a59-376">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="f1a59-377">а второй `@` — для обхода C#.</span><span class="sxs-lookup"><span data-stu-id="f1a59-377">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="f1a59-378">Зарезервированные ключевые слова не используютсяRazor</span><span class="sxs-lookup"><span data-stu-id="f1a59-378">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="f1a59-379">class</span><span class="sxs-lookup"><span data-stu-id="f1a59-379">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="f1a59-380">Проверка класса Razor C#, созданного для представления</span><span class="sxs-lookup"><span data-stu-id="f1a59-380">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f1a59-381">В [ Razor ](xref:razor-pages/sdk) пакет SDK для .NET Core 2,1 или более поздней версии пакет SDK обрабатывает Razor компиляцию файлов.</span><span class="sxs-lookup"><span data-stu-id="f1a59-381">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="f1a59-382">При сборке проекта Razor пакет SDK создает \*obj/<build_configuration>/<target_framework_moniker>/Razor \* каталог в корневом каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="f1a59-382">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="f1a59-383">Структура каталогов в *Razor* каталоге отражает структуру каталогов проекта.</span><span class="sxs-lookup"><span data-stu-id="f1a59-383">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="f1a59-384">Рассмотрим следующую структуру каталогов в проекте ASP.NET Core 2,1 Razor страниц, предназначенном для .net Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="f1a59-384">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="f1a59-385">**Зон**</span><span class="sxs-lookup"><span data-stu-id="f1a59-385">**Areas/**</span></span>
  * <span data-ttu-id="f1a59-386">**Группы**</span><span class="sxs-lookup"><span data-stu-id="f1a59-386">**Admin/**</span></span>
    * <span data-ttu-id="f1a59-387">**См**</span><span class="sxs-lookup"><span data-stu-id="f1a59-387">**Pages/**</span></span>
      * <span data-ttu-id="f1a59-388">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a59-388">*Index.cshtml*</span></span>
      * <span data-ttu-id="f1a59-389">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f1a59-389">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="f1a59-390">**См**</span><span class="sxs-lookup"><span data-stu-id="f1a59-390">**Pages/**</span></span>
  * <span data-ttu-id="f1a59-391">**Используемый**</span><span class="sxs-lookup"><span data-stu-id="f1a59-391">**Shared/**</span></span>
    * <span data-ttu-id="f1a59-392">*_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a59-392">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="f1a59-393">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a59-393">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="f1a59-394">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a59-394">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="f1a59-395">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a59-395">*Index.cshtml*</span></span>
  * <span data-ttu-id="f1a59-396">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f1a59-396">*Index.cshtml.cs*</span></span>

<span data-ttu-id="f1a59-397">При сборке проекта в конфигурации *Отладка* создается следующий каталог *obj*:</span><span class="sxs-lookup"><span data-stu-id="f1a59-397">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="f1a59-398">**расширением**</span><span class="sxs-lookup"><span data-stu-id="f1a59-398">**obj/**</span></span>
  * <span data-ttu-id="f1a59-399">**См**</span><span class="sxs-lookup"><span data-stu-id="f1a59-399">**Debug/**</span></span>
    * <span data-ttu-id="f1a59-400">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="f1a59-400">**netcoreapp2.1/**</span></span>
      * **Razor/**
        * <span data-ttu-id="f1a59-401">**Зон**</span><span class="sxs-lookup"><span data-stu-id="f1a59-401">**Areas/**</span></span>
          * <span data-ttu-id="f1a59-402">**Группы**</span><span class="sxs-lookup"><span data-stu-id="f1a59-402">**Admin/**</span></span>
            * <span data-ttu-id="f1a59-403">**См**</span><span class="sxs-lookup"><span data-stu-id="f1a59-403">**Pages/**</span></span>
              * <span data-ttu-id="f1a59-404">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f1a59-404">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="f1a59-405">**См**</span><span class="sxs-lookup"><span data-stu-id="f1a59-405">**Pages/**</span></span>
          * <span data-ttu-id="f1a59-406">**Используемый**</span><span class="sxs-lookup"><span data-stu-id="f1a59-406">**Shared/**</span></span>
            * <span data-ttu-id="f1a59-407">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f1a59-407">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="f1a59-408">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f1a59-408">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="f1a59-409">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f1a59-409">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="f1a59-410">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f1a59-410">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="f1a59-411">Чтобы просмотреть созданный класс для *pages/index. cshtml*, откройте объект *obj/Debug/netcoreapp 2.1Razor//Пажес/индекс.г.кштмл.КС*.</span><span class="sxs-lookup"><span data-stu-id="f1a59-411">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="f1a59-412">Добавьте в MVC-проект ASP.NET следующий класс:</span><span class="sxs-lookup"><span data-stu-id="f1a59-412">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="f1a59-413">В `Startup.ConfigureServices` переопределите класс `RazorTemplateEngine`, добавленный MVC, классом `CustomTemplateEngine`:</span><span class="sxs-lookup"><span data-stu-id="f1a59-413">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="f1a59-414">Установите точку останова в операторе `return csharpDocument;` класса `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-414">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="f1a59-415">Когда выполнение программы остановится в этой точке, просмотрите значение `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="f1a59-415">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Представление generatedCode в визуализаторе текста](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="f1a59-417">Поиск данных в представлениях и учет регистра</span><span class="sxs-lookup"><span data-stu-id="f1a59-417">View lookups and case sensitivity</span></span>

<span data-ttu-id="f1a59-418">Обработчик Razor представлений выполняет поиск представлений с учетом регистра.</span><span class="sxs-lookup"><span data-stu-id="f1a59-418">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="f1a59-419">Однако фактический поиск зависит от используемой файловой системы.</span><span class="sxs-lookup"><span data-stu-id="f1a59-419">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="f1a59-420">Источники в виде файлов:</span><span class="sxs-lookup"><span data-stu-id="f1a59-420">File based source:</span></span>
  * <span data-ttu-id="f1a59-421">В операционных системах, файловые системы которых не учитывают регистр (например, Windows), поиск поставщика физических файлов не зависит от регистра.</span><span class="sxs-lookup"><span data-stu-id="f1a59-421">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="f1a59-422">Например, поиск по `return View("Test")` выводит совпадения */Views/Home/Test.cshtml*, */Views/home/test.cshtml* и другие варианты с различными сочетаниями регистра.</span><span class="sxs-lookup"><span data-stu-id="f1a59-422">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="f1a59-423">В файловых системах, учитывающих регистр (например, в Linux, OSX и где используется `EmbeddedFileProvider`), поиск выполняется с учетом регистра.</span><span class="sxs-lookup"><span data-stu-id="f1a59-423">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="f1a59-424">Например, поиск по `return View("Test")` дает точное совпадение */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f1a59-424">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="f1a59-425">Предварительно скомпилированные представления: в ASP.NET Core 2.0 и более поздних версиях поиск в предварительно скомпилированных представлениях выполняется без учета регистра во всех операционных системах.</span><span class="sxs-lookup"><span data-stu-id="f1a59-425">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="f1a59-426">Это поведение аналогично поведению поставщика физических файлов в Windows.</span><span class="sxs-lookup"><span data-stu-id="f1a59-426">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="f1a59-427">Если два предварительно скомпилированных представления отличаются только регистром, результат поиска является недетерминированным.</span><span class="sxs-lookup"><span data-stu-id="f1a59-427">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="f1a59-428">Разработчикам рекомендуется использовать для файлов и каталогов тот же регистр, что и для:</span><span class="sxs-lookup"><span data-stu-id="f1a59-428">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="f1a59-429">имен областей, контроллеров и действий;</span><span class="sxs-lookup"><span data-stu-id="f1a59-429">Area, controller, and action names.</span></span>
* Razor<span data-ttu-id="f1a59-430">См.</span><span class="sxs-lookup"><span data-stu-id="f1a59-430"> Pages.</span></span>

<span data-ttu-id="f1a59-431">Совпадающий регистр гарантирует, что развертываемые службы смогут находить свои представления вне зависимости от используемой файловой системы.</span><span class="sxs-lookup"><span data-stu-id="f1a59-431">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1a59-432">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f1a59-432">Additional resources</span></span>

<span data-ttu-id="f1a59-433">[Введение в веб-программирование ASP.NET с Razor использованием синтаксиса](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) предоставляет множество примеров программирования Razor с помощью синтаксиса.</span><span class="sxs-lookup"><span data-stu-id="f1a59-433">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
