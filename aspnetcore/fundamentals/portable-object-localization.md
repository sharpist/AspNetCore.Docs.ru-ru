---
title: 'title: Настройка локализации переносных объектов в ASP.NET Core author: sebastienros description: Эта статья содержит вводные сведения о файлах переносимых объектов и описывает действия по их использованию в приложении ASP.NET Core с помощью платформы Orchard Core.'
author: sebastienros
description: 'ms.author: scaddie ms.date: 26.09.2017 no-loc:'
ms.author: scaddie
ms.date: 09/26/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 57498cc2a773e5147b6eda653cc89d303f238b78
ms.sourcegitcommit: 829dca1d5a7dcccbfe90644101c6be1d1c94ac62
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84355088"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="07632-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="07632-103">'Blazor'</span></span>

<span data-ttu-id="07632-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="07632-104">'Identity'</span></span>

<span data-ttu-id="07632-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="07632-105">'Let's Encrypt'</span></span>

<span data-ttu-id="07632-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="07632-106">'Razor'</span></span> <span data-ttu-id="07632-107">'SignalR' uid: fundamentals/portable-object-localization</span><span class="sxs-lookup"><span data-stu-id="07632-107">'SignalR' uid: fundamentals/portable-object-localization</span></span>

<span data-ttu-id="07632-108">Настройка локализации переносных объектов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07632-108">Configure portable object localization in ASP.NET Core</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="07632-109">Авторы: [Себастьен Рос](https://github.com/sebastienros) (Sébastien Ros) и [Скотт Эдди](https://twitter.com/Scott_Addie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="07632-109">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="07632-110">Эта статья описывает действия по использованию файлов переносимых объектов в приложении ASP.NET Core с помощью платформы [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="07632-110">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span> <span data-ttu-id="07632-111">**Примечание.** Orchard Core не является продуктом корпорации Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="07632-111">**Note:** Orchard Core isn't a Microsoft product.</span></span>
- <span data-ttu-id="07632-112">Поэтому корпорация Майкрософт не предоставляет поддержку для этого компонента.</span><span class="sxs-lookup"><span data-stu-id="07632-112">Consequently, Microsoft provides no support for this feature.</span></span>
- <span data-ttu-id="07632-113">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07632-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="07632-114">Что такое файл переносимых объектов?</span><span class="sxs-lookup"><span data-stu-id="07632-114">What is a PO file?</span></span>
- <span data-ttu-id="07632-115">Файлы переносимых объектов (PO) распространяются как текстовые файлы со строками, переведенными на заданный язык.</span><span class="sxs-lookup"><span data-stu-id="07632-115">PO files are distributed as text files containing the translated strings for a given language.</span></span>

### <a name="example"></a><span data-ttu-id="07632-116">Файлы PO дают определенные преимущества по сравнению с файлами *RESX*, например:</span><span class="sxs-lookup"><span data-stu-id="07632-116">Some advantages of using PO files instead *.resx* files include:</span></span>

<span data-ttu-id="07632-117">Файлы PO поддерживают преобразование во множественную форму, файлы *RESX* — нет.</span><span class="sxs-lookup"><span data-stu-id="07632-117">PO files support pluralization; *.resx* files don't support pluralization.</span></span>

<span data-ttu-id="07632-118">Файлы PO не компилируются подобно файлам *RESX*.</span><span class="sxs-lookup"><span data-stu-id="07632-118">PO files aren't compiled like *.resx* files.</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="07632-119">Поэтому потребность в специальных инструментах и действиях сборки отпадает.</span><span class="sxs-lookup"><span data-stu-id="07632-119">As such, specialized tooling and build steps aren't required.</span></span>

- <span data-ttu-id="07632-120">Файлы PO хорошо работают со средствами редактирования, предназначенными для совместной работы по сети.</span><span class="sxs-lookup"><span data-stu-id="07632-120">PO files work well with collaborative online editing tools.</span></span> <span data-ttu-id="07632-121">Пример</span><span class="sxs-lookup"><span data-stu-id="07632-121">Example</span></span>
- <span data-ttu-id="07632-122">Ниже приведен пример файла PO, содержащего перевод двух строк на французский язык, для одной из которых приведена форма во множественном числе:</span><span class="sxs-lookup"><span data-stu-id="07632-122">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>
- <span data-ttu-id="07632-123">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="07632-123">*fr.po*</span></span>

<span data-ttu-id="07632-124">В этом примере используется следующий синтаксис:</span><span class="sxs-lookup"><span data-stu-id="07632-124">This example uses the following syntax:</span></span>

- <span data-ttu-id="07632-125">`#:`. комментарий, указывающий контекст переводимой строки.</span><span class="sxs-lookup"><span data-stu-id="07632-125">`#:`: A comment indicating the context of the string to be translated.</span></span>
- <span data-ttu-id="07632-126">Одну и ту же строку можно перевести по-разному в зависимости от характера ее использования.</span><span class="sxs-lookup"><span data-stu-id="07632-126">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="07632-127">`msgid`. непереведенная строка.</span><span class="sxs-lookup"><span data-stu-id="07632-127">`msgid`: The untranslated string.</span></span>

<span data-ttu-id="07632-128">`msgstr`. переведенная строка.</span><span class="sxs-lookup"><span data-stu-id="07632-128">`msgstr`: The translated string.</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="07632-129">При поддержке преобразования во множественную форму можно определить дополнительные записи.</span><span class="sxs-lookup"><span data-stu-id="07632-129">In the case of pluralization support, more entries can be defined.</span></span>

<span data-ttu-id="07632-130">`msgid_plural`. непереведенная строка во множественной форме.</span><span class="sxs-lookup"><span data-stu-id="07632-130">`msgid_plural`: The untranslated plural string.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="07632-131">`msgstr[0]`. переведенная строка для случая 0.</span><span class="sxs-lookup"><span data-stu-id="07632-131">`msgstr[0]`: The translated string for the case 0.</span></span>

<span data-ttu-id="07632-132">`msgstr[N]`. переведенная строка для случая N.</span><span class="sxs-lookup"><span data-stu-id="07632-132">`msgstr[N]`: The translated string for the case N.</span></span> <span data-ttu-id="07632-133">Спецификацию файла PO см. [здесь](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="07632-133">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

<span data-ttu-id="07632-134">Настройка поддержки файлов PO в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07632-134">Configuring PO file support in ASP.NET Core</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="07632-135">Этот пример основан на приложении ASP.NET Core MVC, созданном из шаблона проекта Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="07632-135">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

<span data-ttu-id="07632-136">Указание ссылок на пакет</span><span class="sxs-lookup"><span data-stu-id="07632-136">Referencing the package</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="07632-137">Добавьте ссылку на пакет NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="07632-137">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="07632-138">Его можно найти на [MyGet](https://www.myget.org/) в следующем источнике пакетов: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="07632-138">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span> <span data-ttu-id="07632-139">Файл *CSPROJ* теперь содержит строку следующего вида (номер версии может отличаться):</span><span class="sxs-lookup"><span data-stu-id="07632-139">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="07632-140">Регистрация службы</span><span class="sxs-lookup"><span data-stu-id="07632-140">Registering the service</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="07632-141">Добавьте необходимые службы в метод `ConfigureServices` файла *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="07632-141">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

<span data-ttu-id="07632-142">Добавьте необходимое ПО промежуточного слоя в метод `Configure` файла *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="07632-142">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span> <span data-ttu-id="07632-143">Добавьте следующий код в нужное представление Razor.</span><span class="sxs-lookup"><span data-stu-id="07632-143">Add the following code to your Razor view of choice.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="07632-144">В этом примере используется *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="07632-144">*About.cshtml* is used in this example.</span></span> <span data-ttu-id="07632-145">Экземпляр `IViewLocalizer` внедряется и используется для перевода текста "Hello world!".</span><span class="sxs-lookup"><span data-stu-id="07632-145">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span> <span data-ttu-id="07632-146">Создание файла PO</span><span class="sxs-lookup"><span data-stu-id="07632-146">Creating a PO file</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="07632-147">Создайте файл с именем *\<culture code>.po* в корневой папке приложения.</span><span class="sxs-lookup"><span data-stu-id="07632-147">Create a file named *\<culture code>.po* in your application root folder.</span></span>

<span data-ttu-id="07632-148">В этом примере файл имеет имя *fr.po*, так как используется французский язык:</span><span class="sxs-lookup"><span data-stu-id="07632-148">In this example, the file name is *fr.po* because the French language is used:</span></span> <span data-ttu-id="07632-149">Данный файл содержит как строку на перевод, так и строку, переведенную на французский язык.</span><span class="sxs-lookup"><span data-stu-id="07632-149">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="07632-150">При необходимости переводы возвращаются к своим родительским языку и региональным параметрам.</span><span class="sxs-lookup"><span data-stu-id="07632-150">Translations revert to their parent culture, if necessary.</span></span>

<span data-ttu-id="07632-151">В этом примере файл *fr.po* используется, если запрошены язык и региональные параметры `fr-FR` или `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="07632-151">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span> <span data-ttu-id="07632-152">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="07632-152">Testing the application</span></span> <span data-ttu-id="07632-153">Запустите приложение и перейдите по URL-адресу `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="07632-153">Run your application, and navigate to the URL `/Home/About`.</span></span>

## <a name="pluralization"></a><span data-ttu-id="07632-154">Отображается текст **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="07632-154">The text **Hello world!**</span></span>

<span data-ttu-id="07632-155">.</span><span class="sxs-lookup"><span data-stu-id="07632-155">is displayed.</span></span> <span data-ttu-id="07632-156">Перейдите по URL-адресу `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="07632-156">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span>

<span data-ttu-id="07632-157">Отображается текст **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="07632-157">The text **Bonjour le monde!**</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="07632-158">.</span><span class="sxs-lookup"><span data-stu-id="07632-158">is displayed.</span></span>

<span data-ttu-id="07632-159">Преобразование во множественную форму</span><span class="sxs-lookup"><span data-stu-id="07632-159">Pluralization</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="07632-160">Файлы PO поддерживают преобразование во множественную форму, что удобно, когда одну и ту же строку нужно переводить по-разному в зависимости от кратности.</span><span class="sxs-lookup"><span data-stu-id="07632-160">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="07632-161">Эта задача осложняется тем, что каждый язык имеет собственные правила для выбора строк в зависимости от кратности.</span><span class="sxs-lookup"><span data-stu-id="07632-161">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="07632-162">Пакет локализации Orchard предоставляет API для автоматического вызова этих различных форм множественного числа.</span><span class="sxs-lookup"><span data-stu-id="07632-162">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span> <span data-ttu-id="07632-163">Создание файлов PO с множественными формами</span><span class="sxs-lookup"><span data-stu-id="07632-163">Creating pluralization PO files</span></span> <span data-ttu-id="07632-164">Добавьте в указанный ранее файл *fr.po* следующее содержимое:</span><span class="sxs-lookup"><span data-stu-id="07632-164">Add the following content to the previously mentioned *fr.po* file:</span></span>

<span data-ttu-id="07632-165">Сведения о том, что означает каждая запись в этом примере, см. в разделе [Что такое файл переносимых объектов?](#what-is-a-po-file).</span><span class="sxs-lookup"><span data-stu-id="07632-165">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span> <span data-ttu-id="07632-166">Добавление языка с использованием различных форм множественного числа</span><span class="sxs-lookup"><span data-stu-id="07632-166">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="07632-167">В предыдущем примере использовались строки на английском и французском языках.</span><span class="sxs-lookup"><span data-stu-id="07632-167">English and French strings were used in the previous example.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="07632-168">Эти языки имеют всего две формы множественного числа и используют схожие правила. Кратность, равная единице, соответствует первой форме множественного числа.</span><span class="sxs-lookup"><span data-stu-id="07632-168">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="07632-169">Любая другая кратность соответствует второй форме множественного числа.</span><span class="sxs-lookup"><span data-stu-id="07632-169">Any other cardinality is mapped to the second plural form.</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="07632-170">Подобные правила действуют не во всех языках.</span><span class="sxs-lookup"><span data-stu-id="07632-170">Not all languages share the same rules.</span></span> <span data-ttu-id="07632-171">Это показано на примере чешского языка, где форм множественного числа три.</span><span class="sxs-lookup"><span data-stu-id="07632-171">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="07632-172">Создайте файл `cs.po`, как показано ниже, и обратите внимание, что для преобразования во множественную форму нужно три разных перевода:</span><span class="sxs-lookup"><span data-stu-id="07632-172">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

<span data-ttu-id="07632-173">Чтобы принять локализации на чешский язык, добавьте `"cs"` в список поддерживаемых языков и региональных параметров в методе `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="07632-173">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="07632-174">Измените файл *Views/Home/About.cshtml*, чтобы отобразить локализованные строки во множественном числе для разных кратностей:</span><span class="sxs-lookup"><span data-stu-id="07632-174">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="07632-175">**Примечание.** В реальной ситуации для представления этого числа используется переменная.</span><span class="sxs-lookup"><span data-stu-id="07632-175">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="07632-176">Здесь мы повторяем один и тот же код с тремя различными значениями, чтобы описать частный случай.</span><span class="sxs-lookup"><span data-stu-id="07632-176">Here, we repeat the same code with three different values to expose a very specific case.</span></span> <span data-ttu-id="07632-177">После переключения языка и региональных параметров вы увидите следующее:</span><span class="sxs-lookup"><span data-stu-id="07632-177">Upon switching cultures, you see the following:</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="07632-178">Для `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="07632-178">For `/Home/About`:</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="07632-179">Для `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="07632-179">For `/Home/About?culture=fr`:</span></span>

<span data-ttu-id="07632-180">Для `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="07632-180">For `/Home/About?culture=cs`:</span></span> <span data-ttu-id="07632-181">Обратите внимание, что для чешского языка все три перевода различаются.</span><span class="sxs-lookup"><span data-stu-id="07632-181">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="07632-182">На французском и английском языках для двух последних переведенных строк используется одинаковая конструкция.</span><span class="sxs-lookup"><span data-stu-id="07632-182">The French and English cultures share the same construction for the two last translated strings.</span></span> <span data-ttu-id="07632-183">Расширенные задачи</span><span class="sxs-lookup"><span data-stu-id="07632-183">Advanced tasks</span></span>

<span data-ttu-id="07632-184">Учет контекста строк</span><span class="sxs-lookup"><span data-stu-id="07632-184">Contextualizing strings</span></span> <span data-ttu-id="07632-185">Приложения часто содержат переводимые строки в нескольких местах.</span><span class="sxs-lookup"><span data-stu-id="07632-185">Applications often contain the strings to be translated in several places.</span></span>

<span data-ttu-id="07632-186">Одна и та же строка, находящаяся в разных местах приложения (представления Razor или файлы классов), может переводиться по-разному.</span><span class="sxs-lookup"><span data-stu-id="07632-186">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="07632-187">Файл PO поддерживает понятие контекста файла, которое можно использовать для классификации представляемых строк.</span><span class="sxs-lookup"><span data-stu-id="07632-187">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="07632-188">В зависимости от контекста файла (или его отсутствия) строку можно перевести по-разному.</span><span class="sxs-lookup"><span data-stu-id="07632-188">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span> <span data-ttu-id="07632-189">Службы локализации PO используют имя полного класса или представление, используемые при переводе строки.</span><span class="sxs-lookup"><span data-stu-id="07632-189">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span>

<span data-ttu-id="07632-190">Это достигается путем установки значения для записи `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="07632-190">This is accomplished by setting the value on the `msgctxt` entry.</span></span> <span data-ttu-id="07632-191">Рассмотрим небольшое дополнение к предыдущему примеру *fr.po*.</span><span class="sxs-lookup"><span data-stu-id="07632-191">Consider a minor addition to the previous *fr.po* example.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="07632-192">Представление Razor, расположенное в файле *Views/Home/About.cshtml*, можно определить в качестве контекста файла, задав зарезервированное значение записи `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="07632-192">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

<span data-ttu-id="07632-193">При такой настройке `msgctxt` перевод текста отображается при переходе по адресу `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="07632-193">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="07632-194">При переходе по адресу `/Home/Contact?culture=fr-FR` перевод не отображается.</span><span class="sxs-lookup"><span data-stu-id="07632-194">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="07632-195">Если с заданным контекстом файла не совпадает ни одна из конкретных записей, резервный механизм Orchard Core ищет соответствующий файл PO без контекста.</span><span class="sxs-lookup"><span data-stu-id="07632-195">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span>

<span data-ttu-id="07632-196">Если предположить, что для *Views/Home/Contact.cshtml* не задан никакой контекст, при переходе по адресу `/Home/Contact?culture=fr-FR` загружается файл PO, как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="07632-196">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span> <span data-ttu-id="07632-197">Изменение расположения для файлов PO</span><span class="sxs-lookup"><span data-stu-id="07632-197">Changing the location of PO files</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="07632-198">Расположение по умолчанию для файлов PO можно изменить в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="07632-198">The default location of PO files can be changed in `ConfigureServices`:</span></span>

<span data-ttu-id="07632-199">В этом примере файлы PO загружаются из папки *Localization*.</span><span class="sxs-lookup"><span data-stu-id="07632-199">In this example, the PO files are loaded from the *Localization* folder.</span></span> <span data-ttu-id="07632-200">Реализация настраиваемой логики для поиска файлов локализации</span><span class="sxs-lookup"><span data-stu-id="07632-200">Implementing a custom logic for finding localization files</span></span> <span data-ttu-id="07632-201">Когда для поиска файлов PO требуется более сложная логика, можно реализовать интерфейс `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` и зарегистрировать его как службу.</span><span class="sxs-lookup"><span data-stu-id="07632-201">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span>

<span data-ttu-id="07632-202">Это удобно, когда файлы PO могут храниться в различных расположениях или должны находиться в иерархии папок.</span><span class="sxs-lookup"><span data-stu-id="07632-202">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>
