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
ms.openlocfilehash: 2831fd2edd029043e9457cd213e32f1a82c2872e
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424423"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Справочник по синтаксису Razor для ASP.NET Core

По [Рик Андерсон (](https://twitter.com/RickAndMSFT), [Кузнецова Маллен](https://twitter.com/ntaylormullen)и " [викарел](https://github.com/Rabadash8820) "

Razor — это синтаксис разметки для внедрения в веб-страницы серверного кода. Синтаксис Razor состоит из разметки Razor, C# и HTML. Файлы, содержащие Razor, обычно имеют расширение *CSHTML*. Razor также находится в файлах [компонентов Razor](xref:blazor/components) (*.razor*).

## <a name="rendering-html"></a>Отрисовка HTML

Языком Razor по умолчанию является HTML. Отрисовка HTML из разметки Razor ничем не отличается от отрисовки из HTML-файла. Сервер отображает разметку HTML в *CSHTML*-файлах Razor без изменений.

## <a name="razor-syntax"></a>Синтаксис Razor

Razor поддерживает C# и использует символ `@` для перехода с HTML на C#. Razor вычисляет выражения C# и отображает их в выходных данных HTML.

Если за символом `@` следует [зарезервированное ключевое слово Razor](#razor-reserved-keywords), он переходит на разметку Razor. В противном случае он переходит на обычный C#.

В качестве escape-символа для `@` в разметке Razor используйте второй символ `@`:

```cshtml
<p>@@Username</p>
```

Код будет отображен в HTML с одним символом `@`:

```html
<p>@Username</p>
```

HTML-атрибуты и содержимое, включающие адреса электронной почты, не расценивают символ `@` как символ перехода. В следующем примере синтаксический анализ Razor не затрагивает адреса электронной почты:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Неявные выражения Razor

Неявные выражения Razor начинаются с символа `@`, за которым следует код C#:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

Неявные выражения не должны содержать пробелов. Исключением является ключевое слово C# `await`. Если оператор C# имеет четкое окончание, пробелы вставлять можно:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Неявные выражения **не могут** содержать универсальные шаблоны C#, так как символы в угловых скобках (`<>`) интерпретируются как тег HTML. Следующий код является **недопустимым**:

```cshtml
<p>@GenericMethod<int>()</p>
```

Приведенный выше код вызывает ошибку компилятора примерно следующего вида:

* Элемент "int" не был закрыт. Все элементы должны быть самозакрывающимися или иметь соответствующий закрывающий тег.
* Не удается преобразовать группу методов "GenericMethod" в не являющийся делегатом тип "object". Предполагалось вызывать этот метод?

Вызовы универсальных методов должны быть заключены в [явное выражение Razor](#explicit-razor-expressions) или [блок кода Razor](#razor-code-blocks).

## <a name="explicit-razor-expressions"></a>Явные выражения Razor

Явные выражения Razor состоят из символа `@` с соответствующими открывающими и закрывающими скобками. Для отображения времени прошлой недели используется следующая разметка Razor:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Любое содержимое в скобках `@()` вычисляется и отображается в выходных данных.

Неявные выражения, описанные в предыдущем разделе, обычно не содержат пробелов. В следующем коде из значения текущего времени неделя не вычитается:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Код отображает следующий HTML:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Явные выражения позволяют объединять результат своего выполнения с дополнительным текстом:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Без явного выражения `<p>Age@joe.Age</p>` обрабатывается как адрес электронной почты, и на выходе отображается `<p>Age@joe.Age</p>`. Если же текст написан как явное выражение, то вы получите `<p>Age33</p>`.

Вы можете использовать явные выражения для отображения выходных данных из универсальных методов в файлах *CSHTML*. В следующем примере показано, как исправить ошибку, показанную ранее и вызванную скобками в универсальном шаблоне C#. Код записывается как явное выражение:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Кодирование выражений

Выражения C#, которые имеют строковое выходное значение, кодируются в формате HTML. Выражения C#, которые имеют значение `IHtmlContent`, обрабатываются непосредственно с помощью `IHtmlContent.WriteTo`. Выражения C#, которые не имеют выходное значение `IHtmlContent`, преобразуются в строку с помощью `ToString` и кодируются перед обработкой.

```cshtml
@("<span>Hello World</span>")
```

Код отображает следующий HTML:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

HTML отображается в обозревателе следующим образом:

```html
<span>Hello World</span>
```

Выходные данные `HtmlHelper.Raw` не кодируются, но отображаются в виде разметки HTML.

> [!WARNING]
> Использование `HtmlHelper.Raw` с непроверенными входными данными пользователя представляет угрозу безопасности. Эти входные данные могут содержать вредоносный код JavaScript или другие эксплойты. Очистка вводимых пользователем данных является сложной задачей. Старайтесь не использовать `HtmlHelper.Raw` с такими данными.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Код отображает следующий HTML:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Блоки кода Razor

Блоки кода Razor начинаются с символа `@` и заключены в фигурные скобки `{}`. В отличие от выражений код C# внутри блоков кода не обрабатывается. Блоки кода и выражения в представлении используют общую область и определяются в следующем порядке:

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

Код отображает следующий HTML:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

В блоках кода объявите [локальные функции](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) с помощью разметки для использования в качестве методов создания шаблонов:

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

Код отображает следующий HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Неявные переходы

В блоке кода языком по умолчанию является C#, но страница Razor Page может вернуться на HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Явный переход с разделителями

Чтобы определить подраздел блока кода, который должен отрисовывать HTML, окружите подлежащие отображению символы тегами Razor `<text>`:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Используйте этот способ для отрисовки HTML, не заключенного в HTML-теги. Без тега HTML или Razor возникает ошибка времени выполнения Razor.

Тег `<text>` хорошо подходит для контроля пробелов при отрисовке содержимого:

* Отрисовывается только содержимое между тегами `<text>`.
* В выходных данных HTML пробелы до или после тега `<text>` не отображаются.

### <a name="explicit-line-transition"></a>Явный перенос строки

Для отрисовки оставшейся части строки в виде HTML внутри блока кода используйте синтаксис `@:`:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Если в коде отсутствует `@:`, возникает ошибка среды выполнения Razor.

Дополнительные символы `@` в файле Razor могут вызвать ошибки компилятора в последующих операторах блока. Эти ошибки компилятора может быть трудно проанализировать, так как ошибка фактически возникает раньше, чем указано. Чаще всего эта ошибка появляется после объединения множества неявных или явных выражений в один блок кода.

## <a name="control-structures"></a>Управляющие структуры

Управляющие структуры являются расширением блоков кода. Все аспекты блоков кода (переход на разметку, встроенный код C#) также относятся к следующим структурам.

### <a name="conditionals-if-else-if-else-and-switch"></a>Условные выражения \@if, else if, else и \@switch

`@if` контролирует, когда нужно запускать код:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

Для `else` и `else if` символ `@` не требуется:

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

В следующей разметке показано использование оператора switch:

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

### <a name="looping-for-foreach-while-and-do-while"></a>Циклы \@for, \@foreach, \@while и \@do while

Операторы выполнения цикла позволяют выполнять отрисовку шаблонного HTML. Отрисовка списка людей:

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

Поддерживаются следующие операторы выполнения цикла:

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

### <a name="compound-using"></a>Составные \@using

В C# оператор `using` позволяет обеспечить использование какого-то объекта. В Razor этот механизм позволяет создавать вспомогательные функции HTML, включающие дополнительное содержимое. В следующем коде вспомогательные функции HTML используют оператор `@using` для создания тега `<form>`:

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a>\@try, catch, finally

Обработка исключений выполняется так же, как в C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>\@lock

Razor позволяет защищать важные разделы при помощи операторов блокировки:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Комментарии

Razor поддерживает комментарии C# и HTML:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Код отображает следующий HTML:

```html
<!-- HTML comment -->
```

Сервер удаляет комментарии Razor перед отображением веб-страницы. Для разделения комментариев Razor использует `@*  *@`. Следующий код закомментирован, поэтому сервер не отрисовывает разметку:

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Директивы

Директивы Razor представлены неявными выражениями с зарезервированными ключевыми словами после символа `@`. Как правило, директива изменяет способ анализа представления или открывает доступ к дополнительным функциям.

Узнав, каким образом Razor создает код для представления, вы сможете легко понять принципы работы директив.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

Код создает класс, аналогичный следующему:

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

Сведения о просмотре этого класса приводятся в разделе [Просмотр Razor-класса C#, созданного для представления](#inspect-the-razor-c-class-generated-for-a-view) далее в этой статье.

### <a name="attribute"></a>\@attribute

Директива `@attribute` добавляет данный атрибут к классу созданной страницы или представления. В следующем примере добавляется атрибут `[Authorize]`:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a>\@code

*Этот сценарий применяется только к компонентам Razor (.razor).*

Блок `@code` позволяет [компоненту Razor](xref:blazor/components) добавлять в компонент элементы C# (поля, свойства и методы):

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

Для компонентов Razor `@code` является псевдонимом [`@functions`](#functions) и рекомендуется для `@functions` . Допускается более одного блока `@code`.

::: moniker-end

### <a name="functions"></a>\@functions

Директива `@functions` позволяет добавлять элементы C# (поля, свойства и методы) в создаваемый класс:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

В [компонентах Razor](xref:blazor/components) используйте `@code`для добавления членов C# вместо `@functions`.

::: moniker-end

Пример:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Код создает следующую разметку HTML:

```html
<div>From method: Hello</div>
```

Следующий код показывает созданный Razor-класс C#:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

Методы `@functions` служат в качестве методов создания шаблонов при наличии разметки:

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

Код отображает следующий HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a>\@implements

Директива `@implements` реализует интерфейс для созданного класса.

В следующем примере реализуется <xref:System.IDisposable?displayProperty=fullName>, чтобы можно было вызывать метод <xref:System.IDisposable.Dispose*>:

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

### <a name="inherits"></a>\@inherits

Директива `@inherits` позволяет полностью управлять классом, которому наследует представление:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Следующий код показывает настраиваемый тип страницы Razor:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

В представлении отображается `CustomText`:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Код отображает следующий HTML:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` и `@inherits` могут использоваться в одном представлении. `@inherits` может находиться в файле *_ViewImports.cshtml*, который импортируется представлением:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Следующий код показывает пример строго типизированного представления:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Если передать в модель "rick@contoso.com", представление создает следующую разметку HTML:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a>\@inject

Директива `@inject` позволяет странице Razor внедрять в представление службу из [контейнера службы](xref:fundamentals/dependency-injection). Дополнительные сведения: [Внедрение зависимостей в представления](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a>\@layout

*Этот сценарий применяется только к компонентам Razor (.razor).*

Директива `@layout` задает макет для компонента Razor. Компоненты макета используются, чтобы избежать дублирования и несогласованности кода. Для получения дополнительной информации см. <xref:blazor/layouts>.

::: moniker-end

### <a name="model"></a>\@model

*Этот сценарий применяется только к представлениям MVC и Razor Pages (.cshtml).*

Директива `@model` определяет тип модели, передаваемой в представление или страницу:

```cshtml
@model TypeNameOfModel
```

В MVC-приложении ASP.NET Core или Razor Pages, созданном с отдельными учетными записями пользователей, представление *Views/Account/Login.cshtml* содержит следующее объявление модели:

```cshtml
@model LoginViewModel
```

Созданный класс наследует от `RazorPage<dynamic>`:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Для доступа к модели, переданной в представление, Razor предоставляет свойство `Model`:

```cshtml
<div>The Login Email: @Model.Email</div>
```

Директива `@model` задает тип свойства `Model`. Директива указывает `T` в `RazorPage<T>` — созданном классе, на основе которого создается производное представление. Если директива `@model` не указана, свойство `Model` имеет тип `dynamic`. Дополнительные сведения см. [в разделе модели со строгой типизацией и @model ключевое слово](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### <a name="namespace"></a>\@namespace

Директива `@namespace`:

* задает пространство имен класса созданной страницы Razor, представления MVC или компонента Razor.
* Устанавливает корневые производные пространства имен страниц, представлений или классов компонентов из ближайшего файла импорта в дереве каталогов, *_ViewImports.cshtml* (представления или страницы) или *_Imports.razor* (компоненты Razor).

```cshtml
@namespace Your.Namespace.Here
```

Для примера Razor Pages, приведенного в следующей таблице:

* Каждая страница импортирует *Pages/_ViewImports.cshtml*.
* Файл *Pages/_ViewImports.cshtml* содержит `@namespace Hello.World`.
* Каждая страница имеет `Hello.World` в качестве корня пространства имен.

| Страница                                        | Пространство имен                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/index. cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

Описанные отношения применяются к файлам импорта, которые используются в представлениях MVC и компонентах Razor.

Если у нескольких файлов импорта есть директива `@namespace`, для задания корневого пространства имен используется файл, ближайший к странице, компоненту или представлению в дереве каталогов.

Если папка *EvenMorePages* в предыдущем примере содержит файл импорта с `@namespace Another.Planet` (или же файл *Pages/MorePages/EvenMorePages/Page.cshtml* содержит `@namespace Another.Planet`), результат отображается в следующей таблице.

| Страница                                        | Пространство имен               |
| ------------------------------------------- | ----------------------- |
| *Pages/index. cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### <a name="page"></a>\@page

::: moniker range=">= aspnetcore-3.0"

Директива `@page` имеет различные эффекты в зависимости от типа файла, в котором она используется. Директива:

* В *.cshtml*-файле указывает, что файл является страницей Razor. Дополнительные сведения см. в разделе [Пользовательские маршруты](xref:razor-pages/index#custom-routes) и <xref:razor-pages/index>.
* Указывает, что компонент Razor должен выполнять запросы напрямую. Для получения дополнительной информации см. <xref:blazor/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Директива `@page` в первой строке *.cshtml*-файла указывает, что файл является страницей Razor. Для получения дополнительной информации см. <xref:razor-pages/index>.

::: moniker-end

### <a name="section"></a>\@section

*Этот сценарий применяется только к представлениям MVC и Razor Pages (.cshtml).*

Директива `@section` используется в сочетании с [макетами MVC и Razor Pages](xref:mvc/views/layout) и позволяет представлениям и страницам отображать содержимое в различных частях HTML-страницы. Для получения дополнительной информации см. <xref:mvc/views/layout>.

### <a name="using"></a>\@using

Директива `@using` добавляет директиву C# `using` в созданное представление:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

В [компонентах Razor](xref:blazor/components) `@using` также определяет, какие компоненты находятся в области.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Атрибуты директивы

Атрибуты директивы Razor представлены неявными выражениями с зарезервированными ключевыми словами после `@` символа. Атрибут директивы обычно изменяет способ синтаксического анализа элемента или предоставляет различные функциональные возможности.

### <a name="attributes"></a>\@attributes

*Этот сценарий применяется только к компонентам Razor (.razor).*

`@attributes` позволяет компоненту обрабатывать необъявленные атрибуты. Для получения дополнительной информации см. <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.

### <a name="bind"></a>\@bind

*Этот сценарий применяется только к компонентам Razor (.razor).*

Привязка данных в компонентах выполняется с помощью атрибута `@bind`. Для получения дополнительной информации см. <xref:blazor/data-binding>.

### <a name="onevent"></a>\@on{СОБЫТИЕ}

*Этот сценарий применяется только к компонентам Razor (.razor).*

Razor предоставляет функции обработки событий для компонентов. Для получения дополнительной информации см. <xref:blazor/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a>\@on{СОБЫТИЕ}:preventDefault

*Этот сценарий применяется только к компонентам Razor (.razor).*

Запрещает выполнение действия по умолчанию для события.

### <a name="oneventstoppropagation"></a>\@on{СОБЫТИЕ}:stopPropagation

*Этот сценарий применяется только к компонентам Razor (.razor).*

Останавливает распространение события.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a>\@key

*Этот сценарий применяется только к компонентам Razor (.razor).*

Атрибут директивы `@key` заставляет алгоритм сравнения компонентов гарантировать сохранение элементов или компонентов на основе значения ключа. Для получения дополнительной информации см. <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.

### <a name="ref"></a>\@ref

*Этот сценарий применяется только к компонентам Razor (.razor).*

Ссылки на компоненты (`@ref`) предоставляют способ ссылаться на экземпляр компонента, чтобы можно было выполнять команды для этого экземпляра. Для получения дополнительной информации см. <xref:blazor/components#capture-references-to-components>.

### <a name="typeparam"></a>\@typeparam

*Этот сценарий применяется только к компонентам Razor (.razor).*

Директива `@typeparam` объявляет параметр универсального типа для созданного класса компонента. Для получения дополнительной информации см. <xref:blazor/templated-components#generic-typed-components>.

::: moniker-end

## <a name="templated-razor-delegates"></a>Шаблонные делегаты Razor

Шаблоны Razor позволяют определить фрагмент кода пользовательского интерфейса в следующем формате:

```cshtml
@<tag>...</tag>
```

Следующий пример показывает, как указать шаблонный делегат Razor в виде <xref:System.Func%602>. [Динамический тип](/dotnet/csharp/programming-guide/types/using-type-dynamic) указывается для параметра метода, инкапсулируемого делегатом. [Тип объекта](/dotnet/csharp/language-reference/keywords/object) указывается в качестве возвращаемого значения делегата. Этот шаблон используется с <xref:System.Collections.Generic.List%601>объекта `Pet`, имеющим свойство `Name`.

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

Шаблон отрисовывается с использованием `pets`, предоставляемого оператором `foreach`:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Отображенные выходные данные:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Вы также можете предоставить встроенный шаблон Razor в качестве аргумента для метода. В следующем примере метод `Repeat` получает шаблон Razor. Метод использует этот шаблон для создания HTML-содержимого с повторениями элементов из списка:

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

С использованием списка домашних животных из предыдущего примера метод `Repeat` вызывается следующим образом:

* <xref:System.Collections.Generic.List%601> объекта `Pet`.
* Количество повторений для каждого домашнего животного.
* Встроенный шаблон, используемый для перечисления элементов неупорядоченного списка.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Отображенные выходные данные:

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

## <a name="tag-helpers"></a>Вспомогательные функции тегов

*Этот сценарий применяется только к представлениям MVC и Razor Pages (.cshtml).*

Существует три директивы, которые относятся к [вспомогательным функциям тегов](xref:mvc/views/tag-helpers/intro).

| Директива | Функция |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Делает вспомогательные функции тегов доступными в представлении. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Удаляет из представления вспомогательные функции тегов, добавленные ранее. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Задает префикс тега, который активирует поддержку вспомогательной функции тега и ее использования в явном виде. |

## <a name="razor-reserved-keywords"></a>Razorзарезервированные ключевые слова

### <a name="razor-keywords"></a>Razorсловами

* page (требуется ASP.NET Core 2.1 или более поздней версии)
* namespace
* functions
* наследует
* model
* section
* helper (сейчас не поддерживается в ASP.NET Core)

RazorКлючевые слова экранированы с помощью `@(Razor Keyword)` (например, `@(functions)` ).

### <a name="c-razor-keywords"></a>RazorКлючевые слова C#

* case
* do
* default
* для
* foreach
* if
* else
* lock
* switch
* попробуйте выполнить следующее
* catch
* finally
* using
* while

RazorКлючевые слова C# должны быть двойным образом экранированы с помощью `@(@C# Razor Keyword)` (например, `@(@case)` ). Первый заключается в `@` Escape- Razor анализе. а второй `@` — для обхода C#.

### <a name="reserved-keywords-not-used-by-razor"></a>Зарезервированные ключевые слова не используютсяRazor

* класс

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Проверка Razor класса C#, созданного для представления

::: moniker range=">= aspnetcore-2.1"

В пакет SDK для .NET Core 2,1 или более поздней версии [ Razor пакет SDK](xref:razor-pages/sdk) обрабатывает компиляцию Razor файлов. При сборке проекта Razor пакет SDK создает *obj/<build_configuration>/<target_framework_moniker>/ Razor * каталог в корневом каталоге проекта. Структура каталогов в *Razor* каталоге отражает структуру каталогов проекта.

Рассмотрим следующую структуру каталогов в проекте ASP.NET Core 2,1 Razor страниц, предназначенном для .NET Core 2,1:

* **Зон**
  * **Группы**
    * **См**
      * *Index.cshtml*
      * *Index.cshtml.cs*
* **См**
  * **Используемый**
    * *_Layout. cshtml*
  * *_ViewImports.cshtml*
  * *_ViewStart.cshtml*
  * *Index.cshtml*
  * *Index.cshtml.cs*

При сборке проекта в конфигурации *Отладка* создается следующий каталог *obj*:

* **расширением**
  * **См**
    * **netcoreapp2.1/**
      * **Razor/**
        * **Зон**
          * **Группы**
            * **См**
              * *Index.g.cshtml.cs*
        * **См**
          * **Используемый**
            * *_Layout.g.cshtml.cs*
          * *_ViewImports.g.cshtml.cs*
          * *_ViewStart.g.cshtml.cs*
          * *Index.g.cshtml.cs*

Чтобы просмотреть созданный класс для *pages/index. cshtml*, откройте объект *obj/Debug/netcoreapp 2.1/ Razor /Пажес/индекс.г.кштмл.КС*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Добавьте в MVC-проект ASP.NET следующий класс:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

В `Startup.ConfigureServices` переопределите класс `RazorTemplateEngine`, добавленный MVC, классом `CustomTemplateEngine`:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Установите точку останова в операторе `return csharpDocument;` класса `CustomTemplateEngine`. Когда выполнение программы остановится в этой точке, просмотрите значение `generatedCode`.

![Представление generatedCode в визуализаторе текста](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Поиск данных в представлениях и учет регистра

RazorОбработчик представлений выполняет поиск представлений с учетом регистра. Однако фактический поиск зависит от используемой файловой системы.

* Источники в виде файлов:
  * В операционных системах, файловые системы которых не учитывают регистр (например, Windows), поиск поставщика физических файлов не зависит от регистра. Например, поиск по `return View("Test")` выводит совпадения */Views/Home/Test.cshtml*, */Views/home/test.cshtml* и другие варианты с различными сочетаниями регистра.
  * В файловых системах, учитывающих регистр (например, в Linux, OSX и где используется `EmbeddedFileProvider`), поиск выполняется с учетом регистра. Например, поиск по `return View("Test")` дает точное совпадение */Views/Home/Test.cshtml*.
* Предварительно скомпилированные представления: в ASP.NET Core 2.0 и более поздних версиях поиск в предварительно скомпилированных представлениях выполняется без учета регистра во всех операционных системах. Это поведение аналогично поведению поставщика физических файлов в Windows. Если два предварительно скомпилированных представления отличаются только регистром, результат поиска является недетерминированным.

Разработчикам рекомендуется использовать для файлов и каталогов тот же регистр, что и для:

* имен областей, контроллеров и действий;
* RazorСм.

Совпадающий регистр гарантирует, что развертываемые службы смогут находить свои представления вне зависимости от используемой файловой системы.

## <a name="additional-resources"></a>Дополнительные ресурсы

[Введение в веб-программирование ASP.NET с помощью Razor Синтаксис](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) предоставляет множество примеров программирования с помощью Razor синтаксиса.
