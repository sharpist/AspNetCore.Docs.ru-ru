---
title: Часть 8. Добавление проверки
author: rick-anderson
description: Часть 8 серии руководств по Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- Index
- Create
- Delete
- appsettings.json
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: d69ab3452f4f15e916049e5c772a20fe9f9fac65
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570228"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="1a029-103">Часть 8 серии руководств по Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1a029-103">Part 8 of tutorial series on Razor Pages.</span></span>

<span data-ttu-id="1a029-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="1a029-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1a029-105">В этом разделе к модели `Movie` добавляется логика проверки.</span><span class="sxs-lookup"><span data-stu-id="1a029-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="1a029-106">Правила проверки применяются каждый раз, когда пользователь создает или редактирует фильм.</span><span class="sxs-lookup"><span data-stu-id="1a029-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="1a029-107">Проверка</span><span class="sxs-lookup"><span data-stu-id="1a029-107">Validation</span></span>

<span data-ttu-id="1a029-108">Ключевой принцип разработки программного обеспечения называется [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (от английского "**D** on't **R** epeat **Y** ourself" — не повторяйся).</span><span class="sxs-lookup"><span data-stu-id="1a029-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="1a029-109">При разработке Razor Pages рекомендуется задавать любые функциональные возможности лишь один раз и затем при необходимости отражать их в рамках всего приложения.</span><span class="sxs-lookup"><span data-stu-id="1a029-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="1a029-110">Принцип "Не повторяйся" может помочь:</span><span class="sxs-lookup"><span data-stu-id="1a029-110">DRY can help:</span></span>

* <span data-ttu-id="1a029-111">сократить объем кода в приложении;</span><span class="sxs-lookup"><span data-stu-id="1a029-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="1a029-112">снизить вероятность возникновения ошибки в коде и упростить его тестирование и поддержку.</span><span class="sxs-lookup"><span data-stu-id="1a029-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="1a029-113">Ярким примером применения принципа "Не повторяйся" является поддержка проверки, реализуемая в Razor Pages и на платформе Entity Framework:</span><span class="sxs-lookup"><span data-stu-id="1a029-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle:</span></span>

* <span data-ttu-id="1a029-114">Правила проверки декларативно определяются в одном месте, в классе модели.</span><span class="sxs-lookup"><span data-stu-id="1a029-114">Validation rules are declaratively specified in one place, in the model class.</span></span>
* <span data-ttu-id="1a029-115">Правила применяются по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="1a029-115">Rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="1a029-116">Добавление правил проверки к модели фильма</span><span class="sxs-lookup"><span data-stu-id="1a029-116">Add validation rules to the movie model</span></span>

<span data-ttu-id="1a029-117">Пространство имен `DataAnnotations` предоставляет:</span><span class="sxs-lookup"><span data-stu-id="1a029-117">The `DataAnnotations` namespace provides:</span></span>

* <span data-ttu-id="1a029-118">Набор встроенных атрибутов проверки, которые декларативно применяются к классу или свойству.</span><span class="sxs-lookup"><span data-stu-id="1a029-118">A set of built-in validation attributes that are applied declaratively to a class or property.</span></span>
* <span data-ttu-id="1a029-119">Атрибуты форматирования (такие как `[DataType]`), которые обеспечивают форматирование и не предназначены для проверки.</span><span class="sxs-lookup"><span data-stu-id="1a029-119">Formatting attributes like `[DataType]` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="1a029-120">Обновите класс `Movie`, чтобы использовать преимущества встроенных атрибутов проверки `[Required]`, `[StringLength]`, `[RegularExpression]` и `[Range]`.</span><span class="sxs-lookup"><span data-stu-id="1a029-120">Update the `Movie` class to take advantage of the built-in `[Required]`, `[StringLength]`, `[RegularExpression]`, and `[Range]` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="1a029-121">Атрибуты проверки определяют поведение для свойств модели, к которым они применяются:</span><span class="sxs-lookup"><span data-stu-id="1a029-121">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="1a029-122">Атрибуты `[Required]` и `[MinimumLength]` означают, что свойство должно иметь значение.</span><span class="sxs-lookup"><span data-stu-id="1a029-122">The `[Required]` and `[MinimumLength]` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="1a029-123">Пользователь может свободно вводить пробелы для выполнения этой проверки.</span><span class="sxs-lookup"><span data-stu-id="1a029-123">Nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="1a029-124">Атрибут `[RegularExpression]` ограничивает набор допустимых для ввода символов.</span><span class="sxs-lookup"><span data-stu-id="1a029-124">The `[RegularExpression]` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="1a029-125">В приведенном выше коде в `Genre`:</span><span class="sxs-lookup"><span data-stu-id="1a029-125">In the preceding code, `Genre`:</span></span>

  * <span data-ttu-id="1a029-126">должны использоваться только буквы;</span><span class="sxs-lookup"><span data-stu-id="1a029-126">Must only use letters.</span></span>
  * <span data-ttu-id="1a029-127">первая буква должна быть прописной;</span><span class="sxs-lookup"><span data-stu-id="1a029-127">The first letter is required to be uppercase.</span></span> <span data-ttu-id="1a029-128">пробелы, цифры и специальные символы не допускаются.</span><span class="sxs-lookup"><span data-stu-id="1a029-128">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="1a029-129">`RegularExpression` `Rating`:</span><span class="sxs-lookup"><span data-stu-id="1a029-129">The `RegularExpression` `Rating`:</span></span>

  * <span data-ttu-id="1a029-130">первый символ должен быть прописной буквой;</span><span class="sxs-lookup"><span data-stu-id="1a029-130">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="1a029-131">допускаются специальные символы и цифры, а также последующие пробелы.</span><span class="sxs-lookup"><span data-stu-id="1a029-131">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="1a029-132">значение "PG-13" допустимо для рейтинга, но недопустимо для `Genre`;</span><span class="sxs-lookup"><span data-stu-id="1a029-132">"PG-13" is valid for a rating, but fails for a `Genre`.</span></span>

* <span data-ttu-id="1a029-133">атрибут `[Range]` ограничивает значения указанным диапазоном.</span><span class="sxs-lookup"><span data-stu-id="1a029-133">The `[Range]` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="1a029-134">Атрибут `[StringLength]` может задавать максимальную и, при необходимости, минимальную длину строкового свойства.</span><span class="sxs-lookup"><span data-stu-id="1a029-134">The `[StringLength]` attribute can set a maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="1a029-135">Типы значений (например, `decimal`, `int`, `float`, `DateTime`) по своей природе являются обязательными и не требуют атрибута `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="1a029-135">Value types, such as `decimal`, `int`, `float`, `DateTime`, are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="1a029-136">Указанные выше правила проверки используются для демонстрации, они не являются оптимальными для рабочей системы.</span><span class="sxs-lookup"><span data-stu-id="1a029-136">The preceding validation rules are used for demonstration, they are not optimal for a production system.</span></span> <span data-ttu-id="1a029-137">Например, предыдущее исключение не позволяет ввести модель Movie с двумя символами и не допускает специальные знаки в `Genre`.</span><span class="sxs-lookup"><span data-stu-id="1a029-137">For example, the preceding prevents entering a movie with only two chars and doesn't allow special characters in `Genre`.</span></span>

<span data-ttu-id="1a029-138">Наличие правил проверки, которые автоматически применяются ASP.NET Core, помогает:</span><span class="sxs-lookup"><span data-stu-id="1a029-138">Having validation rules automatically enforced by ASP.NET Core helps:</span></span>

* <span data-ttu-id="1a029-139">повысить степень надежности приложения;</span><span class="sxs-lookup"><span data-stu-id="1a029-139">Helps make the app more robust.</span></span>
* <span data-ttu-id="1a029-140">сократить возможность сохранения недопустимых данных в базе данных.</span><span class="sxs-lookup"><span data-stu-id="1a029-140">Reduce chances of saving invalid data to the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="1a029-141">Пользовательский интерфейс проверки ошибок в Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1a029-141">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="1a029-142">Запустите приложение и перейдите в раздел "Pages/Movies" (Страницы/фильмы).</span><span class="sxs-lookup"><span data-stu-id="1a029-142">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="1a029-143">Выберите ссылку **Create New** (Создать).</span><span class="sxs-lookup"><span data-stu-id="1a029-143">Select the **Create New** link.</span></span> <span data-ttu-id="1a029-144">Введите в форму какие-либо недопустимые значения.</span><span class="sxs-lookup"><span data-stu-id="1a029-144">Complete the form with some invalid values.</span></span> <span data-ttu-id="1a029-145">Если функция проверки jQuery на стороне клиента обнаруживает ошибку, сведения о ней отображаются в соответствующем сообщении.</span><span class="sxs-lookup"><span data-stu-id="1a029-145">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Форма просмотра фильма с несколькими ошибками проверки jQuery на стороне клиента](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="1a029-147">Обратите внимание, что для каждого поля, содержащего недопустимое значение, в форме автоматически отображается сообщение об ошибке проверки.</span><span class="sxs-lookup"><span data-stu-id="1a029-147">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="1a029-148">Эти ошибки применяются как на стороне клиента (с помощью JavaScript и jQuery), так и на стороне сервера (если пользователь отключает JavaScript).</span><span class="sxs-lookup"><span data-stu-id="1a029-148">The errors are enforced both client-side, using JavaScript and jQuery, and server-side, when a user has JavaScript disabled.</span></span>

<span data-ttu-id="1a029-149">Основным преимуществом является то, что на страницах Create (Создание) или Edit (Изменение) **не требуется** изменять код.</span><span class="sxs-lookup"><span data-stu-id="1a029-149">A significant benefit is that **no** code changes were necessary in the Create or Edit pages.</span></span> <span data-ttu-id="1a029-150">Пользовательский интерфейс проверки активируется после применения к модели атрибутов проверки достоверности.</span><span class="sxs-lookup"><span data-stu-id="1a029-150">Once data annotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="1a029-151">В Razor Pages, создаваемом в рамках этого руководства, правила проверки применяются автоматически (для этого к свойствам класса модели `Movie` применяются атрибуты проверки).</span><span class="sxs-lookup"><span data-stu-id="1a029-151">The Razor Pages created in this tutorial automatically picked up the validation rules, using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="1a029-152">При проверке страницы редактирования применяются те же правила.</span><span class="sxs-lookup"><span data-stu-id="1a029-152">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="1a029-153">Данные формы передаются на сервер только после того, как будут устранены все ошибки проверки на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="1a029-153">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="1a029-154">Чтобы убедиться, что данные формы не отправляются, используйте любой из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="1a029-154">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="1a029-155">Поместите точку останова в метод `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="1a029-155">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="1a029-156">Отправьте форму, выбрав **Create** (Создать) или **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="1a029-156">Submit the form by selecting **Create** or **Save**.</span></span> <span data-ttu-id="1a029-157">Точка останова не достигается ни при каких обстоятельствах.</span><span class="sxs-lookup"><span data-stu-id="1a029-157">The break point is never hit.</span></span>
* <span data-ttu-id="1a029-158">Используйте [инструмент Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="1a029-158">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="1a029-159">Проследите сетевой трафик с помощью инструментов разработчика для браузера.</span><span class="sxs-lookup"><span data-stu-id="1a029-159">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="1a029-160">Проверка на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="1a029-160">Server-side validation</span></span>

<span data-ttu-id="1a029-161">Если в браузере отключен JavaScript, форма с ошибками отправляется на сервер.</span><span class="sxs-lookup"><span data-stu-id="1a029-161">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="1a029-162">Реализация проверки на стороне сервера:</span><span class="sxs-lookup"><span data-stu-id="1a029-162">Optional, test server-side validation:</span></span>

1. <span data-ttu-id="1a029-163">Отключите JavaScript в браузере.</span><span class="sxs-lookup"><span data-stu-id="1a029-163">Disable JavaScript in the browser.</span></span> <span data-ttu-id="1a029-164">JavaScript можно отключить с помощью средств разработчика в браузере.</span><span class="sxs-lookup"><span data-stu-id="1a029-164">JavaScript can be disabled using browser's developer tools.</span></span> <span data-ttu-id="1a029-165">Если JavaScript невозможно отключить в этом браузере, попробуйте использовать другой браузер.</span><span class="sxs-lookup"><span data-stu-id="1a029-165">If JavaScript cannot be disabled in the browser, try another browser.</span></span>
1. <span data-ttu-id="1a029-166">Поместите точку останова в метод `OnPostAsync` страниц Create(Создание) или Edit (Изменение).</span><span class="sxs-lookup"><span data-stu-id="1a029-166">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
1. <span data-ttu-id="1a029-167">Отправьте форму с недопустимыми данными.</span><span class="sxs-lookup"><span data-stu-id="1a029-167">Submit a form with invalid data.</span></span>
1. <span data-ttu-id="1a029-168">Проверка недопустимого состояния модели:</span><span class="sxs-lookup"><span data-stu-id="1a029-168">Verify the model state is invalid:</span></span>

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
<span data-ttu-id="1a029-169">Иначе [отключите проверку на стороне клиента на сервере](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="1a029-169">Alternatively, [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="1a029-170">В следующем коде демонстрируется часть страницы *Create.cshtml* , созданной ранее в рамках этого руководства.</span><span class="sxs-lookup"><span data-stu-id="1a029-170">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="1a029-171">Она используется на страницах Create (Создание) и Edit (Изменение) для:</span><span class="sxs-lookup"><span data-stu-id="1a029-171">It's used by the Create and Edit pages to:</span></span>

* <span data-ttu-id="1a029-172">Отображения начальной формы.</span><span class="sxs-lookup"><span data-stu-id="1a029-172">Display the initial form.</span></span>
* <span data-ttu-id="1a029-173">Повторного отображения формы в случае ошибки.</span><span class="sxs-lookup"><span data-stu-id="1a029-173">Redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="1a029-174">[Вспомогательная функция тега Input](xref:mvc/views/working-with-forms) использует атрибуты [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) и создает HTML-атрибуты, необходимые для проверки jQuery на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="1a029-174">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="1a029-175">[Вспомогательная функция тега Validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) отображает ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="1a029-175">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="1a029-176">Дополнительные сведения см. в разделе [Проверка](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="1a029-176">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="1a029-177">На страницах Create (Создание) и Edit (Редактирование) не определены правила проверки.</span><span class="sxs-lookup"><span data-stu-id="1a029-177">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="1a029-178">Правила проверки и строки ошибок указываются только в классе `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1a029-178">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="1a029-179">Они автоматически применяются к Razor Pages, которые редактируют модель `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1a029-179">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="1a029-180">Любые необходимые изменения логики проверки осуществляются исключительно в модели.</span><span class="sxs-lookup"><span data-stu-id="1a029-180">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="1a029-181">Проверка применяется согласованно на уровне всего приложения, для чего логика проверки определяется в одном месте.</span><span class="sxs-lookup"><span data-stu-id="1a029-181">Validation is applied consistently throughout the application, validation logic is defined in one place.</span></span> <span data-ttu-id="1a029-182">Такой подход позволяет максимально оптимизировать код и упростить его поддержку и обновление.</span><span class="sxs-lookup"><span data-stu-id="1a029-182">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="use-datatype-attributes"></a><span data-ttu-id="1a029-183">Использование атрибутов DataType</span><span class="sxs-lookup"><span data-stu-id="1a029-183">Use DataType Attributes</span></span>

<span data-ttu-id="1a029-184">Проверьте класс `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1a029-184">Examine the `Movie` class.</span></span> <span data-ttu-id="1a029-185">В пространстве имен `System.ComponentModel.DataAnnotations` в дополнение к набору встроенных атрибутов проверки предоставляются атрибуты форматирования.</span><span class="sxs-lookup"><span data-stu-id="1a029-185">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="1a029-186">Атрибут `[DataType]` применяется к свойствам `ReleaseDate` и `Price`.</span><span class="sxs-lookup"><span data-stu-id="1a029-186">The `[DataType]` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="1a029-187">Атрибуты `[DataType]` предоставляют:</span><span class="sxs-lookup"><span data-stu-id="1a029-187">The `[DataType]` attributes provide:</span></span>

* <span data-ttu-id="1a029-188">Указания для обработчика представлений для форматирования данных.</span><span class="sxs-lookup"><span data-stu-id="1a029-188">Hints for the view engine to format the data.</span></span>
* <span data-ttu-id="1a029-189">Атрибуты, например `<a>` для URL-адресов и `<a href="mailto:EmailAddress.com">` для электронной почты.</span><span class="sxs-lookup"><span data-stu-id="1a029-189">Supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span>

<span data-ttu-id="1a029-190">Используйте атрибут `[RegularExpression]` для проверки формата данных.</span><span class="sxs-lookup"><span data-stu-id="1a029-190">Use the `[RegularExpression]` attribute to validate the format of the data.</span></span> <span data-ttu-id="1a029-191">Атрибут `[DataType]` позволяет указать тип данных с более точным определением по сравнению со встроенным типом базы данных.</span><span class="sxs-lookup"><span data-stu-id="1a029-191">The `[DataType]` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="1a029-192">Атрибуты`[DataType]` не предназначены для проверки.</span><span class="sxs-lookup"><span data-stu-id="1a029-192">`[DataType]` attributes aren't validation attributes.</span></span> <span data-ttu-id="1a029-193">В том же приложении отображается только дата (без времени).</span><span class="sxs-lookup"><span data-stu-id="1a029-193">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="1a029-194">Перечисление `DataType` предоставляет множество типов данных, таких как `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress` и т. д.</span><span class="sxs-lookup"><span data-stu-id="1a029-194">The `DataType` enumeration provides many data types, such as `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, and more.</span></span> 

<span data-ttu-id="1a029-195">Атрибуты `[DataType]`:</span><span class="sxs-lookup"><span data-stu-id="1a029-195">The `[DataType]` attributes:</span></span>

* <span data-ttu-id="1a029-196">Обеспечивают автоматическое предоставление функций для определенных типов в приложении.</span><span class="sxs-lookup"><span data-stu-id="1a029-196">Can enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="1a029-197">Например, можно создавать ссылку `mailto:` для `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="1a029-197">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="1a029-198">Могут предоставить селектор даты `DataType.Date` в браузерах, поддерживающих HTML5.</span><span class="sxs-lookup"><span data-stu-id="1a029-198">Can provide a date selector `DataType.Date` in browsers that support HTML5.</span></span>
* <span data-ttu-id="1a029-199">Создают атрибуты HTML 5 `data-`, которые используются браузерами с поддержкой HTML 5.</span><span class="sxs-lookup"><span data-stu-id="1a029-199">Emit HTML 5 `data-`, pronounced "data dash", attributes that HTML 5 browsers consume.</span></span>
* <span data-ttu-id="1a029-200">**Не предназначены** для проверки.</span><span class="sxs-lookup"><span data-stu-id="1a029-200">Do **not** provide any validation.</span></span>

<span data-ttu-id="1a029-201">`DataType.Date` не задает формат отображаемой даты.</span><span class="sxs-lookup"><span data-stu-id="1a029-201">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="1a029-202">По умолчанию поле данных отображается с использованием форматов, установленных в параметрах `CultureInfo` сервера.</span><span class="sxs-lookup"><span data-stu-id="1a029-202">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="1a029-203">Требуются заметки к данным `[Column(TypeName = "decimal(18, 2)")]`, чтобы Entity Framework Core корректно сопоставила `Price` с валютой в базе данных.</span><span class="sxs-lookup"><span data-stu-id="1a029-203">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="1a029-204">Дополнительные сведения см. в разделе [Типы данных](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="1a029-204">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="1a029-205">С помощью атрибута `[DisplayFormat]` можно явно указать формат даты:</span><span class="sxs-lookup"><span data-stu-id="1a029-205">The `[DisplayFormat]` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="1a029-206">Параметр `ApplyFormatInEditMode` указывает, что при отображении значения для редактирования будет применяться форматирование.</span><span class="sxs-lookup"><span data-stu-id="1a029-206">The `ApplyFormatInEditMode` setting specifies that the formatting will be applied when the value is displayed for editing.</span></span> <span data-ttu-id="1a029-207">Это поведение может быть нежелательным для некоторых полей.</span><span class="sxs-lookup"><span data-stu-id="1a029-207">That behavior may not be wanted for some fields.</span></span> <span data-ttu-id="1a029-208">Например, в полях валюты в пользовательском интерфейсе редактирования использовать символ денежной единицы, как правило, не требуется.</span><span class="sxs-lookup"><span data-stu-id="1a029-208">For example, in currency values, the currency symbol is usually not wanted in the edit UI.</span></span>

<span data-ttu-id="1a029-209">Атрибут `[DisplayFormat]` может использоваться отдельно, однако чаще всего его рекомендуется применять вместе с атрибутом `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="1a029-209">The `[DisplayFormat]` attribute can be used by itself, but it's generally a good idea to use the `[DataType]` attribute.</span></span> <span data-ttu-id="1a029-210">Атрибут `[DataType]` передает семантику данных (в отличие от способа их вывода на экран).</span><span class="sxs-lookup"><span data-stu-id="1a029-210">The `[DataType]` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="1a029-211">Атрибут `[DataType]` дает следующие преимущества, недоступные в `[DisplayFormat]`:</span><span class="sxs-lookup"><span data-stu-id="1a029-211">The `[DataType]` attribute provides the following benefits that aren't available with `[DisplayFormat]`:</span></span>

* <span data-ttu-id="1a029-212">Поддержка функций HTML5 в браузере, например отображение элемента управления календарем, соответствующего языковому стандарту символа валюты, ссылок электронной почты и т. д.</span><span class="sxs-lookup"><span data-stu-id="1a029-212">The browser can enable HTML5 features, for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.</span></span>
* <span data-ttu-id="1a029-213">По умолчанию формат отображения данных в браузере определяется в соответствии с установленным языковым стандартом.</span><span class="sxs-lookup"><span data-stu-id="1a029-213">By default, the browser renders data using the correct format based on its locale.</span></span>
* <span data-ttu-id="1a029-214">Атрибут `[DataType]` обеспечивает поддержку платформы ASP.NET Core для выбора соответствующего шаблона поля, применяемого при отображении данных.</span><span class="sxs-lookup"><span data-stu-id="1a029-214">The `[DataType]` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="1a029-215">При отдельном использовании атрибут `DisplayFormat` базируется на строковом шаблоне.</span><span class="sxs-lookup"><span data-stu-id="1a029-215">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="1a029-216">**Примечание**. Проверка jQuery не работает с атрибутом `[Range]` и `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="1a029-216">**Note:** jQuery validation doesn't work with the `[Range]` attribute and `DateTime`.</span></span> <span data-ttu-id="1a029-217">Например, следующий код всегда приводит к возникновению ошибки проверки на стороне клиента, даже если дата попадает в указанный диапазон:</span><span class="sxs-lookup"><span data-stu-id="1a029-217">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="1a029-218">Лучшей методикой будет не компилировать модели с фиксированными датами, поэтому использовать атрибуты `[Range]` и `DateTime` следует крайне осторожно.</span><span class="sxs-lookup"><span data-stu-id="1a029-218">It's a best practice to avoid compiling hard dates in models, so using the `[Range]` attribute and `DateTime` is discouraged.</span></span> <span data-ttu-id="1a029-219">Используйте [конфигурацию](xref:fundamentals/configuration/index) для диапазонов дат и других значений, подверженных частым изменениям, а не указывайте их в коде.</span><span class="sxs-lookup"><span data-stu-id="1a029-219">Use [Configuration](xref:fundamentals/configuration/index) for date ranges and other values that are subject to frequent change rather than specifying it in code.</span></span>

<span data-ttu-id="1a029-220">В следующем коде демонстрируется объединение атрибутов в одной строке:</span><span class="sxs-lookup"><span data-stu-id="1a029-220">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="1a029-221">Дополнительные операции EF Core с Razor Pages см. в статье [Начало работы с Razor Pages и EF Core](xref:data/ef-rp/intro).</span><span class="sxs-lookup"><span data-stu-id="1a029-221">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="1a029-222">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="1a029-222">Apply migrations</span></span>

<span data-ttu-id="1a029-223">DataAnnotation, примененные к классу, изменяют схему.</span><span class="sxs-lookup"><span data-stu-id="1a029-223">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="1a029-224">Например, DataAnnotation, примеренные к полю `Title`, выполняют следующее:</span><span class="sxs-lookup"><span data-stu-id="1a029-224">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="1a029-225">ограничивают число символов до 60;</span><span class="sxs-lookup"><span data-stu-id="1a029-225">Limits the characters to 60.</span></span>
* <span data-ttu-id="1a029-226">не допускают значение `null`.</span><span class="sxs-lookup"><span data-stu-id="1a029-226">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1a029-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a029-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1a029-228">Сейчас таблица `Movie` имеет следующую схему:</span><span class="sxs-lookup"><span data-stu-id="1a029-228">The `Movie` table currently has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="1a029-229">Предыдущие изменения схемы не приводят к созданию исключения EF.</span><span class="sxs-lookup"><span data-stu-id="1a029-229">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="1a029-230">Но следует создать миграцию, чтобы схема соответствовала модели.</span><span class="sxs-lookup"><span data-stu-id="1a029-230">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="1a029-231">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="1a029-231">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="1a029-232">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="1a029-232">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="1a029-233">`Update-Database` выполняет методы `Up` класса `New_DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="1a029-233">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="1a029-234">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="1a029-234">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="1a029-235">Обновленная таблица `Movie` имеет следующую схему:</span><span class="sxs-lookup"><span data-stu-id="1a029-235">The updated `Movie` table has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1a029-236">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="1a029-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1a029-237">Для SQLite миграция не требуется.</span><span class="sxs-lookup"><span data-stu-id="1a029-237">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="1a029-238">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="1a029-238">Publish to Azure</span></span>

<span data-ttu-id="1a029-239">Сведения о развертывании в Azure, см. в разделе [Учебник: Создание приложения ASP.NET Core в Azure с подключением к базе данных SQL](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span><span class="sxs-lookup"><span data-stu-id="1a029-239">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

<span data-ttu-id="1a029-240">Благодарим вас за изучение общих сведений о страницах Razor.</span><span class="sxs-lookup"><span data-stu-id="1a029-240">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="1a029-241">Отличным дополнением к этому руководству является руководство по [началу работы с Razor Pages и EF Core](xref:data/ef-rp/intro).</span><span class="sxs-lookup"><span data-stu-id="1a029-241">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1a029-242">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1a029-242">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="1a029-243">Предыдущая статья. Добавление нового поля</span><span class="sxs-lookup"><span data-stu-id="1a029-243">Previous: Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
