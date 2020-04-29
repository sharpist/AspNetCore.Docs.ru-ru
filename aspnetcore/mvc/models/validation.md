---
title: Проверка модели в ASP.NET Core MVC
author: rick-anderson
description: Сведения о проверке модели в ASP.NET Core MVC и Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: mvc/models/validation
ms.openlocfilehash: 0e3d4f4705dbfdae00943de2d85c603b6762a2f8
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205895"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="9bac6-103">Проверка модели в ASP.NET Core MVC и Razor Pages</span><span class="sxs-lookup"><span data-stu-id="9bac6-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9bac6-104">Автор: [Кирк Ларкин (Kirk Larkin)](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="9bac6-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="9bac6-105">В этой статье объясняется, как осуществлять проверки вводимых пользователем данных в приложении ASP.NET Core MVC или Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9bac6-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="9bac6-106">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9bac6-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="9bac6-107">Состояние модели</span><span class="sxs-lookup"><span data-stu-id="9bac6-107">Model state</span></span>

<span data-ttu-id="9bac6-108">Состояние модели представляет ошибки, создаваемые двумя подсистемами: привязкой модели и проверкой модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="9bac6-109">Ошибки [привязки модели](model-binding.md) обычно являются ошибками преобразования данных.</span><span class="sxs-lookup"><span data-stu-id="9bac6-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="9bac6-110">Например, в целочисленном поле указывается "x".</span><span class="sxs-lookup"><span data-stu-id="9bac6-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="9bac6-111">Проверка модели происходит после ее привязки. В процессе сообщается об ошибках несоответствия данных бизнес-правилам.</span><span class="sxs-lookup"><span data-stu-id="9bac6-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="9bac6-112">Например, в поле, которое ожидает оценку от 1 до 5, указывается 0.</span><span class="sxs-lookup"><span data-stu-id="9bac6-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="9bac6-113">Привязка и проверка модели происходят перед выполнением действия контроллера или метода обработчика Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9bac6-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="9bac6-114">Веб-приложение отвечает за проверку `ModelState.IsValid` и реагирует соответствующим образом.</span><span class="sxs-lookup"><span data-stu-id="9bac6-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="9bac6-115">Веб-приложения обычно повторно отображают страницы с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="9bac6-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="9bac6-116">Контроллерам веб-API не нужно проверять `ModelState.IsValid` при наличии атрибута `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="9bac6-117">В этом случае автоматически возвращается ответ HTTP 400, содержащий сведения об ошибке, если состояние модели недопустимо.</span><span class="sxs-lookup"><span data-stu-id="9bac6-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="9bac6-118">Дополнительные сведения см. в разделе [Автоматические отклики HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="9bac6-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="9bac6-119">Перезапуск проверки</span><span class="sxs-lookup"><span data-stu-id="9bac6-119">Rerun validation</span></span>

<span data-ttu-id="9bac6-120">Проверка выполняется автоматически, однако может потребоваться повторить ее вручную.</span><span class="sxs-lookup"><span data-stu-id="9bac6-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="9bac6-121">Например, можно вычислить значение свойства и повторно выполнить проверку после установки свойства в вычисляемое значение.</span><span class="sxs-lookup"><span data-stu-id="9bac6-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="9bac6-122">Для повторной проверки вызовите метод `TryValidateModel`, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="9bac6-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="9bac6-123">Атрибуты проверки</span><span class="sxs-lookup"><span data-stu-id="9bac6-123">Validation attributes</span></span>

<span data-ttu-id="9bac6-124">Атрибуты проверки позволяют задать правила проверки для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="9bac6-125">В следующем примере из примера приложения показан класс модели, который помечается с помощью атрибутов проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="9bac6-126">`[ClassicMovie]` является настраиваемым атрибутом проверки; остальные являются встроенными.</span><span class="sxs-lookup"><span data-stu-id="9bac6-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="9bac6-127">Не показан `[ClassicMovieWithClientValidator]`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="9bac6-128">`[ClassicMovieWithClientValidator]` демонстрирует альтернативный способ реализации настраиваемого атрибута.</span><span class="sxs-lookup"><span data-stu-id="9bac6-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="9bac6-129">Встроенные атрибуты</span><span class="sxs-lookup"><span data-stu-id="9bac6-129">Built-in attributes</span></span>

<span data-ttu-id="9bac6-130">Ниже приведены некоторые из встроенных атрибутов проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="9bac6-131">`[CreditCard]`: Проверяет, что свойство имеет формат кредитной карты.</span><span class="sxs-lookup"><span data-stu-id="9bac6-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="9bac6-132">`[Compare]`: Проверяет соответствие двух свойств в модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-132">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="9bac6-133">`[EmailAddress]`: Проверяет, что свойство имеет формат электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9bac6-133">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="9bac6-134">`[Phone]`: Проверяет, что свойство имеет формат номера телефона.</span><span class="sxs-lookup"><span data-stu-id="9bac6-134">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="9bac6-135">`[Range]`: Проверяет, попадает значение свойства в указанный диапазон.</span><span class="sxs-lookup"><span data-stu-id="9bac6-135">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="9bac6-136">`[RegularExpression]`: Проверяет соответствие значения свойства указанному регулярному выражению.</span><span class="sxs-lookup"><span data-stu-id="9bac6-136">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="9bac6-137">`[Required]`: Проверяет, что поле не имеет значение null.</span><span class="sxs-lookup"><span data-stu-id="9bac6-137">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="9bac6-138">Дополнительные сведения о поведении этого атрибута см. в разделе [ `[Required]` Attribute](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="9bac6-138">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="9bac6-139">`[StringLength]`: Проверяет, что значение свойства строки не превышает заданное ограничение длины.</span><span class="sxs-lookup"><span data-stu-id="9bac6-139">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="9bac6-140">`[Url]`: Проверяет, что свойство имеет формат URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="9bac6-140">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="9bac6-141">`[Remote]`: Проверяет входные данные на клиенте, вызывая метод действия на сервере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-141">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="9bac6-142">Дополнительные сведения о поведении этого атрибута см. в разделе [ `[Remote]` Attribute](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="9bac6-142">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="9bac6-143">Полный перечень атрибутов проверки можно найти в пространстве имен [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="9bac6-143">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="9bac6-144">Сообщения об ошибках</span><span class="sxs-lookup"><span data-stu-id="9bac6-144">Error messages</span></span>

<span data-ttu-id="9bac6-145">Атрибуты проверки позволяют указать сообщение об ошибке, которое будет отображаться, если входные данные недопустимы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-145">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="9bac6-146">Пример:</span><span class="sxs-lookup"><span data-stu-id="9bac6-146">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="9bac6-147">На внутреннем уровне атрибуты вызывают `String.Format` с заполнителем для имени поля и иногда дополнительным заполнителями.</span><span class="sxs-lookup"><span data-stu-id="9bac6-147">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="9bac6-148">Пример:</span><span class="sxs-lookup"><span data-stu-id="9bac6-148">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="9bac6-149">При применении к свойству `Name` сообщение об ошибке, созданное в приведенном выше коде, имело бы вид Name length must be between 6 and 8 (Длина имени должна быть от 6 до 8).</span><span class="sxs-lookup"><span data-stu-id="9bac6-149">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="9bac6-150">Чтобы узнать, какие параметры передаются в `String.Format` для сообщения об ошибке определенного атрибута, см. раздел [Исходный код DataAnnotations](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="9bac6-150">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="9bac6-151">Атрибут [Required]</span><span class="sxs-lookup"><span data-stu-id="9bac6-151">[Required] attribute</span></span>

<span data-ttu-id="9bac6-152">Система проверки в .NET Core 3.0 и более поздних версий рассматривает не допускающие значение NULL параметры или свойства привязки так, как если бы они имели атрибут `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-152">The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="9bac6-153">[Типы значений](/dotnet/csharp/language-reference/keywords/value-types), например `decimal` и `int`, не поддерживают значение NULL.</span><span class="sxs-lookup"><span data-stu-id="9bac6-153">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span> <span data-ttu-id="9bac6-154">Это поведение можно отключить с помощью параметра <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9bac6-154">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="9bac6-155">Проверка [Required] на сервере</span><span class="sxs-lookup"><span data-stu-id="9bac6-155">[Required] validation on the server</span></span>

<span data-ttu-id="9bac6-156">На сервере обязательное значение считается отсутствующим, если свойство имеет значение NULL.</span><span class="sxs-lookup"><span data-stu-id="9bac6-156">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="9bac6-157">Поле, не допускающее значения NULL, всегда является допустимым, и сообщение об ошибке атрибута `[Required]` никогда не выводится.</span><span class="sxs-lookup"><span data-stu-id="9bac6-157">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="9bac6-158">Тем не менее привязка модели для ненулевого свойства может завершиться ошибкой, приводящей к сообщению об ошибке, например `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-158">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="9bac6-159">Чтобы задать настраиваемое сообщение об ошибке во время проверки не допускающих значения NULL типов на стороне сервера, у вас есть следующие варианты.</span><span class="sxs-lookup"><span data-stu-id="9bac6-159">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="9bac6-160">Сделать поле допускающим значение NULL (например, `decimal?` вместо `decimal`).</span><span class="sxs-lookup"><span data-stu-id="9bac6-160">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="9bac6-161">Типы значений [Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) обрабатываются как стандартные типы, допускающие значение NULL.</span><span class="sxs-lookup"><span data-stu-id="9bac6-161">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="9bac6-162">Указать сообщение об ошибке по умолчанию для использования в привязке модели, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-162">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="9bac6-163">Дополнительные сведения об ошибках привязки модели, у которых можно задать сообщение по умолчанию, см. в разделе <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="9bac6-163">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="9bac6-164">Проверка [Required] на клиенте</span><span class="sxs-lookup"><span data-stu-id="9bac6-164">[Required] validation on the client</span></span>

<span data-ttu-id="9bac6-165">Типы и строки, не допускающие значение NULL, обрабатываются на клиенте не так, как на сервере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-165">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="9bac6-166">На клиенте:</span><span class="sxs-lookup"><span data-stu-id="9bac6-166">On the client:</span></span>

* <span data-ttu-id="9bac6-167">Значение считается присутствующим только в том случае, если для него вводятся данные.</span><span class="sxs-lookup"><span data-stu-id="9bac6-167">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="9bac6-168">Таким образом, проверка на стороне клиента обрабатывает типы, не допускающие значение NULL, так же, как обнуляемые типы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-168">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="9bac6-169">Пробелы в поле строки считаются допустимыми входными данными при проверке методом jQuery [required](https://jqueryvalidation.org/required-method/).</span><span class="sxs-lookup"><span data-stu-id="9bac6-169">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="9bac6-170">Проверка на стороне сервера считает, что обязательное строковое поле недопустимо, если введены только пробелы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-170">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="9bac6-171">Как отмечалось ранее, не допускающие значение NULL типы рассматриваются как имеющие атрибут `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-171">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="9bac6-172">Это означает, что вы получаете проверку на стороне клиента, даже если не применять атрибут `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-172">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="9bac6-173">Но если вы не используете атрибут, вы получаете сообщение об ошибке по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="9bac6-173">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="9bac6-174">Чтобы задать настраиваемое сообщение об ошибке, используйте атрибут.</span><span class="sxs-lookup"><span data-stu-id="9bac6-174">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="9bac6-175">Атрибут [Remote]</span><span class="sxs-lookup"><span data-stu-id="9bac6-175">[Remote] attribute</span></span>

<span data-ttu-id="9bac6-176">Атрибут `[Remote]` реализует проверку на стороне клиента, в ходе которой требуется вызвать метод на сервере, чтобы определить, является ли допустимым поле ввода.</span><span class="sxs-lookup"><span data-stu-id="9bac6-176">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="9bac6-177">Например, приложению может потребоваться проверить, занято ли имя пользователя.</span><span class="sxs-lookup"><span data-stu-id="9bac6-177">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="9bac6-178">Для реализации удаленной проверки сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="9bac6-178">To implement remote validation:</span></span>

1. <span data-ttu-id="9bac6-179">Создайте для вызова из JavaScript метод действия.</span><span class="sxs-lookup"><span data-stu-id="9bac6-179">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="9bac6-180">Метод проверки jQuery [remote](https://jqueryvalidation.org/remote-method/) ожидает ответ JSON.</span><span class="sxs-lookup"><span data-stu-id="9bac6-180">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="9bac6-181">`true` означает, что входные данные допустимы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-181">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="9bac6-182">`false`, `undefined` или `null` означают, что входные данные недопустимы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-182">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="9bac6-183">Вывод стандартного сообщения об ошибке</span><span class="sxs-lookup"><span data-stu-id="9bac6-183">Display the default error message.</span></span>
   * <span data-ttu-id="9bac6-184">Все прочие значения означают, что входные данные недопустимы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-184">Any other string means the input is invalid.</span></span> <span data-ttu-id="9bac6-185">Вывод строки как настраиваемого сообщения об ошибке.</span><span class="sxs-lookup"><span data-stu-id="9bac6-185">Display the string as a custom error message.</span></span>

   <span data-ttu-id="9bac6-186">Ниже приведен пример метода действия, который возвращает настраиваемое сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="9bac6-186">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="9bac6-187">В классе модели пометьте свойство атрибутом `[Remote]`, указывающим метод действия проверки, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-187">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="9bac6-188">Атрибут `[Remote]` находится в пространстве имен `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-188">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="9bac6-189">Дополнительные поля</span><span class="sxs-lookup"><span data-stu-id="9bac6-189">Additional fields</span></span>

<span data-ttu-id="9bac6-190">Свойство `AdditionalFields` атрибута `[Remote]` позволяет проверять сочетания полей с данными на сервере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-190">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="9bac6-191">Например, если бы в модели `User` были свойства `FirstName` и `LastName`, могла бы возникнуть необходимость проверить, нет ли уже пользователя с такой парой имен.</span><span class="sxs-lookup"><span data-stu-id="9bac6-191">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="9bac6-192">В следующем примере показано использование `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-192">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="9bac6-193">`AdditionalFields` можно явно присвоить строкам "FirstName" и "LastName", но использование оператора [nameof](/dotnet/csharp/language-reference/keywords/nameof) упрощает дальнейший рефакторинг.</span><span class="sxs-lookup"><span data-stu-id="9bac6-193">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="9bac6-194">Методу действия для этой проверки необходимо принимать аргументы `firstName` и `lastName`</span><span class="sxs-lookup"><span data-stu-id="9bac6-194">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="9bac6-195">Когда пользователь вводит имя или фамилию, JavaScript выполняет удаленный вызов, чтобы увидеть, будет ли эта пара принята.</span><span class="sxs-lookup"><span data-stu-id="9bac6-195">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="9bac6-196">Чтобы проверить несколько дополнительных полей, их следует указывать в виде списка с разделителями-запятыми.</span><span class="sxs-lookup"><span data-stu-id="9bac6-196">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="9bac6-197">Например, чтобы добавить в модель свойство `MiddleName`, задайте атрибут `[Remote]`, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-197">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="9bac6-198">`AdditionalFields`, как и все аргументы атрибутов, должен представлять собой константное выражение.</span><span class="sxs-lookup"><span data-stu-id="9bac6-198">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="9bac6-199">Поэтому не следует использовать [интерполированную строку](/dotnet/csharp/language-reference/keywords/interpolated-strings) или вызов <xref:System.String.Join*>для инициализации `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-199">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="9bac6-200">Альтернативы для встроенных атрибутов</span><span class="sxs-lookup"><span data-stu-id="9bac6-200">Alternatives to built-in attributes</span></span>

<span data-ttu-id="9bac6-201">Если вам нужна проверка, которую не предоставляют встроенные атрибуты, вы можете следующее.</span><span class="sxs-lookup"><span data-stu-id="9bac6-201">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="9bac6-202">[Создать настраиваемые атрибуты](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="9bac6-202">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="9bac6-203">[Реализовать IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="9bac6-203">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="9bac6-204">Настраиваемые атрибуты</span><span class="sxs-lookup"><span data-stu-id="9bac6-204">Custom attributes</span></span>

<span data-ttu-id="9bac6-205">Для сценариев, где не годятся встроенные атрибуты проверки, можно создать настраиваемые атрибуты.</span><span class="sxs-lookup"><span data-stu-id="9bac6-205">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="9bac6-206">Создайте класс, наследуемый от <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, и переопределите метод <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="9bac6-206">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="9bac6-207">Метод `IsValid` принимает объект с именем *value*, который является входными данными для проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-207">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="9bac6-208">Перегрузка также принимает объект `ValidationContext`, который предоставляет дополнительные сведения, такие как экземпляр модели, созданный с помощью привязки модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-208">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="9bac6-209">В следующем примере проверяется, что дата выпуска фильмов в *классическом* жанре задана не позднее указанного года.</span><span class="sxs-lookup"><span data-stu-id="9bac6-209">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="9bac6-210">Атрибут `[ClassicMovie]`:</span><span class="sxs-lookup"><span data-stu-id="9bac6-210">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="9bac6-211">выполняется только на сервере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-211">Is only run on the server.</span></span>
* <span data-ttu-id="9bac6-212">Для классических фильмов проверяет дату выпуска:</span><span class="sxs-lookup"><span data-stu-id="9bac6-212">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="9bac6-213">Приведенная выше переменная `movie` представляет объект `Movie`, который содержит данные из переданной формы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-213">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="9bac6-214">Если проверка завершается неудачно, возвращается `ValidationResult` с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="9bac6-214">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="9bac6-215">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="9bac6-215">IValidatableObject</span></span>

<span data-ttu-id="9bac6-216">Предыдущий пример работает только с типами `Movie`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-216">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="9bac6-217">Другой вариант для проверки на уровне класса — реализация `IValidatableObject` в классе модели, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-217">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="9bac6-218">Проверка узлов верхнего уровня</span><span class="sxs-lookup"><span data-stu-id="9bac6-218">Top-level node validation</span></span>

<span data-ttu-id="9bac6-219">Узлы верхнего уровня содержат:</span><span class="sxs-lookup"><span data-stu-id="9bac6-219">Top-level nodes include:</span></span>

* <span data-ttu-id="9bac6-220">параметры действия;</span><span class="sxs-lookup"><span data-stu-id="9bac6-220">Action parameters</span></span>
* <span data-ttu-id="9bac6-221">свойства контроллера;</span><span class="sxs-lookup"><span data-stu-id="9bac6-221">Controller properties</span></span>
* <span data-ttu-id="9bac6-222">параметры обработчика страниц;</span><span class="sxs-lookup"><span data-stu-id="9bac6-222">Page handler parameters</span></span>
* <span data-ttu-id="9bac6-223">свойства страничной модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-223">Page model properties</span></span>

<span data-ttu-id="9bac6-224">Проверка привязанных к модели узлов верхнего уровня осуществляется наряду с проверкой свойств модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-224">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="9bac6-225">В следующем примере, взятом из примера приложения, метод `VerifyPhone` использует класс <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> для проверки параметра действия `phone`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-225">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="9bac6-226">Узлы верхнего уровня могут применять класс <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> с атрибутами проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-226">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="9bac6-227">В следующем примере, взятом из примера приложения, метод `CheckAge` указывает, что при отправке формы параметр `age` должен быть привязан из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="9bac6-227">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="9bac6-228">На странице "Check Age" (Проверка возраста) (*CheckAge.cshtml*) находятся две формы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-228">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="9bac6-229">Первая форма отправляет значение `Age`, равное `99`, в виде параметра строки запроса: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-229">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="9bac6-230">Если из строки запроса отправлен параметр `age` в правильном формате, форма проходит проверку.</span><span class="sxs-lookup"><span data-stu-id="9bac6-230">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="9bac6-231">Вторая форма на странице "Check Age" (Проверка возраста) отправляет значение `Age` в теле запроса, и проверка завершается сбоем.</span><span class="sxs-lookup"><span data-stu-id="9bac6-231">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="9bac6-232">Ошибка привязки связана с тем, что параметр `age` должен поступать из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="9bac6-232">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="9bac6-233">Максимальное количество ошибок</span><span class="sxs-lookup"><span data-stu-id="9bac6-233">Maximum errors</span></span>

<span data-ttu-id="9bac6-234">При достижении максимального количества ошибок (по умолчанию 200) проверка прекращается.</span><span class="sxs-lookup"><span data-stu-id="9bac6-234">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="9bac6-235">Это число можно изменить с помощью следующего кода в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9bac6-235">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="9bac6-236">Максимальная рекурсия</span><span class="sxs-lookup"><span data-stu-id="9bac6-236">Maximum recursion</span></span>

<span data-ttu-id="9bac6-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> проходит через граф объектов в проверяемой модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="9bac6-238">У глубоких моделей, содержащих бесконечную рекурсию, в ходе проверки может произойти переполнение стека.</span><span class="sxs-lookup"><span data-stu-id="9bac6-238">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="9bac6-239">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) предоставляет способ остановить проверку до превышения настроенной глубины рекурсии обхода.</span><span class="sxs-lookup"><span data-stu-id="9bac6-239">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="9bac6-240">Значение `MvcOptions.MaxValidationDepth` по умолчанию —32.</span><span class="sxs-lookup"><span data-stu-id="9bac6-240">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="9bac6-241">Автоматическое сокращение</span><span class="sxs-lookup"><span data-stu-id="9bac6-241">Automatic short-circuit</span></span>

<span data-ttu-id="9bac6-242">Проверка автоматически сокращается (пропускается), если граф модели не требует проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-242">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="9bac6-243">К числу объектов, которые среда выполнения пропускает при проверке, относятся коллекции примитивов (такие как `byte[]`, `string[]`, `Dictionary<string, string>`) и сложные графы объектов, которые не имеют проверяющих элементов управления.</span><span class="sxs-lookup"><span data-stu-id="9bac6-243">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="9bac6-244">Отключение проверки</span><span class="sxs-lookup"><span data-stu-id="9bac6-244">Disable validation</span></span>

<span data-ttu-id="9bac6-245">Чтобы отключить проверку, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="9bac6-245">To disable validation:</span></span>

1. <span data-ttu-id="9bac6-246">Создайте реализацию интерфейса `IObjectModelValidator`, которая не помечает поля как недопустимые.</span><span class="sxs-lookup"><span data-stu-id="9bac6-246">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="9bac6-247">Добавьте следующий код в `Startup.ConfigureServices` для замены реализации `IObjectModelValidator` по умолчанию в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="9bac6-247">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="9bac6-248">По-прежнему могут отображаться ошибки состояния модели, которые идут из привязки модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-248">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="9bac6-249">Проверка на стороне клиента</span><span class="sxs-lookup"><span data-stu-id="9bac6-249">Client-side validation</span></span>

<span data-ttu-id="9bac6-250">Проверка на стороне клиента не позволяет отправлять форму, пока ее данные не будут допустимыми.</span><span class="sxs-lookup"><span data-stu-id="9bac6-250">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="9bac6-251">При нажатии кнопки "Отправить" выполняется код JavaScript, который либо отправляет форму, либо выводит сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="9bac6-251">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="9bac6-252">Проверка на стороне клиента позволяет избежать ненужного кругового захода на сервер при наличии ошибки ввода в форме.</span><span class="sxs-lookup"><span data-stu-id="9bac6-252">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="9bac6-253">Ссылки на следующий скрипт в *_Layout.cshtml* и *_ValidationScriptsPartial.cshtml* поддерживают проверку на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="9bac6-253">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="9bac6-254">Скрипт [ненавязчивой проверки jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) — это настраиваемая интерфейсная библиотека Майкрософт, которая основана на популярном подключаемом модуле [jQuery Validate](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="9bac6-254">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="9bac6-255">Без скрипта ненавязчивой проверки jQuery одну и ту же логику проверки приходилось бы реализовывать в двух местах: в атрибутах проверки для свойств модели на стороне сервера, а затем еще раз в скриптах на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="9bac6-255">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="9bac6-256">Вместо этого [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и [вспомогательные функции HTML](xref:mvc/views/overview) могут использовать атрибуты проверки и метаданные типов из свойств модели для обработки атрибутов `data-` HTML 5 в элементах форм, требующих проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-256">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="9bac6-257">Скрипт ненавязчивой проверки jQuery анализирует эти атрибуты `data-` и передает логику в подключаемый модуль jQuery Validate, по сути копируя логику проверки на стороне сервера в клиент.</span><span class="sxs-lookup"><span data-stu-id="9bac6-257">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="9bac6-258">Ошибки проверки могут выводиться в клиенте с помощью вспомогательных функций тегов, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="9bac6-258">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="9bac6-259">Приведенные выше вспомогательные функции тегов отрисовывают следующий код HTML:</span><span class="sxs-lookup"><span data-stu-id="9bac6-259">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="9bac6-260">Обратите внимание на то, что атрибуты `data-` в выходных данных HTML соответствуют атрибутам проверки для свойства `Movie.ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-260">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="9bac6-261">Атрибут `data-val-required` содержит сообщение об ошибке, которое выводится, если пользователь не заполнил поле даты выхода.</span><span class="sxs-lookup"><span data-stu-id="9bac6-261">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="9bac6-262">Скрипт ненавязчивой проверки jQuery передает это значение в метод [required()](https://jqueryvalidation.org/required-method/) подключаемого модуля jQuery Validate, который затем выводит это сообщение в соответствующем элементе **\<span>**.</span><span class="sxs-lookup"><span data-stu-id="9bac6-262">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="9bac6-263">Проверка типа данных основана на типе свойства в .NET, если его не переопределяет атрибут `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-263">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="9bac6-264">Браузеры имеют свои сообщения об по умолчанию, но пакет ненавязчивой проверки jQuery может переопределять эти сообщения.</span><span class="sxs-lookup"><span data-stu-id="9bac6-264">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="9bac6-265">Атрибуты и подклассы `[DataType]`, такие как `[EmailAddress]`, позволяют указать сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="9bac6-265">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="9bac6-266">Ненавязчивая проверка</span><span class="sxs-lookup"><span data-stu-id="9bac6-266">Unobtrusive validation</span></span>

<span data-ttu-id="9bac6-267">См. сведения о ненавязчивой проверке в этой [проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span><span class="sxs-lookup"><span data-stu-id="9bac6-267">For information on unobtrusive validation, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="9bac6-268">Добавление проверки к динамическим формам</span><span class="sxs-lookup"><span data-stu-id="9bac6-268">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="9bac6-269">Скрипт ненавязчивой проверки jQuery передает логику и параметры проверки в подключаемый модуль jQuery Validate при первой загрузке страницы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-269">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="9bac6-270">Поэтому динамически создаваемые формы не подвергаются проверке автоматически.</span><span class="sxs-lookup"><span data-stu-id="9bac6-270">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="9bac6-271">Чтобы включить проверку, необходимо указать, что скрипт ненавязчивой проверки jQuery должен анализировать динамическую форму сразу после ее создания.</span><span class="sxs-lookup"><span data-stu-id="9bac6-271">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="9bac6-272">Например, приведенный ниже код показывает, как можно настроить проверку на стороне клиента для формы, добавленной посредством AJAX.</span><span class="sxs-lookup"><span data-stu-id="9bac6-272">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="9bac6-273">Метод `$.validator.unobtrusive.parse()` принимает селектор jQuery в качестве единственного аргумента.</span><span class="sxs-lookup"><span data-stu-id="9bac6-273">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="9bac6-274">Этот метод предписывает скрипту ненавязчивой проверки jQuery анализировать атрибуты `data-` форм в этом селекторе.</span><span class="sxs-lookup"><span data-stu-id="9bac6-274">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="9bac6-275">Значения этих атрибутов затем передаются в подключаемый модуль jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="9bac6-275">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="9bac6-276">Добавление проверки к динамическим элементам управления</span><span class="sxs-lookup"><span data-stu-id="9bac6-276">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="9bac6-277">Метод `$.validator.unobtrusive.parse()` обрабатывает всю форму, а не отдельные динамически создаваемые элементы управления, такие как `<input>` и `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-277">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="9bac6-278">Для повторной обработки формы удалите данные проверки, которые были добавлены при анализе формы ранее, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="9bac6-278">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="9bac6-279">Настраиваемая проверка на стороне клиента</span><span class="sxs-lookup"><span data-stu-id="9bac6-279">Custom client-side validation</span></span>

<span data-ttu-id="9bac6-280">Настраиваемая проверка на стороне клиента выполняется путем создания атрибутов HTML `data-`, которые работают с настраиваемым адаптером проверки jQuery.</span><span class="sxs-lookup"><span data-stu-id="9bac6-280">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="9bac6-281">В следующем примере кода адаптера используются атрибуты `[ClassicMovie]` и `[ClassicMovieWithClientValidator]`, которые были введены ранее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="9bac6-281">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="9bac6-282">Сведения о способах создания адаптеров см. в [документации по проверкам jQuery](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="9bac6-282">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="9bac6-283">Использование адаптера для заданного поля инициируется атрибутами `data-`, которые:</span><span class="sxs-lookup"><span data-stu-id="9bac6-283">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="9bac6-284">помечают поле как проходящее проверку (`data-val="true"`);</span><span class="sxs-lookup"><span data-stu-id="9bac6-284">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="9bac6-285">указывают имя правила проверки и текст сообщения об ошибке (например, `data-val-rulename="Error message."`);</span><span class="sxs-lookup"><span data-stu-id="9bac6-285">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="9bac6-286">указывают любые дополнительные параметры, в которых нуждается проверяющий элемент управления (например, `data-val-rulename-param1="value"`).</span><span class="sxs-lookup"><span data-stu-id="9bac6-286">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="9bac6-287">В следующем примере показаны атрибуты `data-` для нашего атрибута `ClassicMovie` из примера приложения.</span><span class="sxs-lookup"><span data-stu-id="9bac6-287">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="9bac6-288">Как отмечалось ранее, [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и [вспомогательные методы HTML](xref:mvc/views/overview) используют сведения из атрибутов проверки для подготовки к отрисовке атрибутов `data-`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-288">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="9bac6-289">Существует два варианта для написания кода, который приводит к созданию настраиваемых атрибутов HTML `data-`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-289">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="9bac6-290">Создайте класс, производный от `AttributeAdapterBase<TAttribute>`, и класс, реализующий `IValidationAttributeAdapterProvider`; зарегистрируйте атрибут и его адаптер в функции внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="9bac6-290">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="9bac6-291">Этот метод следует за [участником с единственной обязанностью](https://wikipedia.org/wiki/Single_responsibility_principle) в том, что код проверки, связанный с сервером и клиентом, выделен в отдельные классы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-291">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="9bac6-292">Адаптер также имеет следующее преимущество: так как он зарегистрирован в функции внедрения зависимостей, для него при необходимости доступны другие службы в ней.</span><span class="sxs-lookup"><span data-stu-id="9bac6-292">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="9bac6-293">Реализуйте `IClientModelValidator` в вашем классе `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-293">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="9bac6-294">Этот метод стоит использовать, если атрибут не выполняет проверку на стороне сервера и не нуждается в службах из функции внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="9bac6-294">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="9bac6-295">AttributeAdapter для проверки на стороне клиента</span><span class="sxs-lookup"><span data-stu-id="9bac6-295">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="9bac6-296">Этот метод отрисовки атрибутов `data-` в формате HTML используется атрибутом `ClassicMovie` в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="9bac6-296">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="9bac6-297">Чтобы добавить клиентскую проверку с помощью этого метода, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="9bac6-297">To add client validation by using this method:</span></span>

1. <span data-ttu-id="9bac6-298">Создайте класс адаптера атрибута для настраиваемого атрибута проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-298">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="9bac6-299">Наследуйте класс от [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="9bac6-299">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="9bac6-300">Создайте метод `AddValidation`, который добавляет атрибуты `data-` для выводимых данных, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-300">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="9bac6-301">Создайте класс поставщика адаптера, который реализует <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="9bac6-301">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="9bac6-302">В методе `GetAttributeAdapter` передайте настраиваемый атрибут в конструктор адаптера, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-302">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="9bac6-303">Зарегистрируйте поставщик адаптера для внедрения зависимостей в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-303">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="9bac6-304">IClientModelValidator для проверки на стороне клиента</span><span class="sxs-lookup"><span data-stu-id="9bac6-304">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="9bac6-305">Этот метод отрисовки атрибутов `data-` в формате HTML используется атрибутом `ClassicMovieWithClientValidator` в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="9bac6-305">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="9bac6-306">Чтобы добавить клиентскую проверку с помощью этого метода, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="9bac6-306">To add client validation by using this method:</span></span>

* <span data-ttu-id="9bac6-307">В настраиваемом атрибуте проверки реализуйте интерфейс `IClientModelValidator` и создайте метод `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-307">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="9bac6-308">В метод `AddValidation` добавьте атрибуты `data-` для проверки, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-308">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="9bac6-309">Отключение проверки на стороне клиента</span><span class="sxs-lookup"><span data-stu-id="9bac6-309">Disable client-side validation</span></span>

<span data-ttu-id="9bac6-310">Следующий код отключает клиентскую проверку в Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="9bac6-310">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="9bac6-311">Другие параметры отключения проверки на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="9bac6-311">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="9bac6-312">Закомментируйте ссылку на `_ValidationScriptsPartial` во всех файлах с расширением *CSHTML*.</span><span class="sxs-lookup"><span data-stu-id="9bac6-312">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="9bac6-313">Удалите содержимое файла *Pages\Shared\_ValidationScriptsPartial.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9bac6-313">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="9bac6-314">Предыдущий подход не помешает проверке на стороне клиента библиотеки классов Razor для ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="9bac6-314">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="9bac6-315">Для получения дополнительной информации см. <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="9bac6-315">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9bac6-316">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9bac6-316">Additional resources</span></span>

* [<span data-ttu-id="9bac6-317">Пространство имен System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="9bac6-317">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="9bac6-318">Привязка модели</span><span class="sxs-lookup"><span data-stu-id="9bac6-318">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9bac6-319">В этой статье объясняется, как осуществлять проверки вводимых пользователем данных в приложении ASP.NET Core MVC или Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9bac6-319">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="9bac6-320">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9bac6-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="9bac6-321">Состояние модели</span><span class="sxs-lookup"><span data-stu-id="9bac6-321">Model state</span></span>

<span data-ttu-id="9bac6-322">Состояние модели представляет ошибки, создаваемые двумя подсистемами: привязкой модели и проверкой модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-322">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="9bac6-323">Ошибки, поступающие из [привязки модели](model-binding.md), чаще всего представляют собой ошибки преобразования данных (например, x вводится в поле, которое ожидает целое число).</span><span class="sxs-lookup"><span data-stu-id="9bac6-323">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="9bac6-324">Проверка модели проводится после привязки модели и сообщает об ошибках, при которых данные не соответствуют бизнес-правилам (например, 0 вводится в поле, которое ожидает оценку от 1 до 5).</span><span class="sxs-lookup"><span data-stu-id="9bac6-324">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="9bac6-325">Привязка модели и проверка модели происходят перед выполнением действия контроллера или метода обработчика Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9bac6-325">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="9bac6-326">Веб-приложение отвечает за проверку `ModelState.IsValid` и реагирует соответствующим образом.</span><span class="sxs-lookup"><span data-stu-id="9bac6-326">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="9bac6-327">Веб-приложения обычно повторно отображают страницы с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="9bac6-327">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="9bac6-328">Контроллерам веб-API не нужно проверять `ModelState.IsValid` при наличии атрибута `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-328">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="9bac6-329">В этом случае автоматически возвращается ответ HTTP 400, содержащий сведения об ошибке, если состояние модели недопустимо.</span><span class="sxs-lookup"><span data-stu-id="9bac6-329">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="9bac6-330">Дополнительные сведения см. в разделе [Автоматические отклики HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="9bac6-330">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="9bac6-331">Перезапуск проверки</span><span class="sxs-lookup"><span data-stu-id="9bac6-331">Rerun validation</span></span>

<span data-ttu-id="9bac6-332">Проверка выполняется автоматически, однако может потребоваться повторить ее вручную.</span><span class="sxs-lookup"><span data-stu-id="9bac6-332">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="9bac6-333">Например, можно вычислить значение свойства и повторно выполнить проверку после установки свойства в вычисляемое значение.</span><span class="sxs-lookup"><span data-stu-id="9bac6-333">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="9bac6-334">Для повторной проверки вызовите метод `TryValidateModel`, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="9bac6-334">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="9bac6-335">Атрибуты проверки</span><span class="sxs-lookup"><span data-stu-id="9bac6-335">Validation attributes</span></span>

<span data-ttu-id="9bac6-336">Атрибуты проверки позволяют задать правила проверки для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-336">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="9bac6-337">В следующем примере из [примера приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) показан класс модели, который помечается с помощью атрибутов проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-337">The following example from [the sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="9bac6-338">`[ClassicMovie]` является настраиваемым атрибутом проверки; остальные являются встроенными.</span><span class="sxs-lookup"><span data-stu-id="9bac6-338">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="9bac6-339">Не показан `[ClassicMovie2]`, который демонстрирует альтернативный способ реализации настраиваемого атрибута.</span><span class="sxs-lookup"><span data-stu-id="9bac6-339">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="9bac6-340">Встроенные атрибуты</span><span class="sxs-lookup"><span data-stu-id="9bac6-340">Built-in attributes</span></span>

<span data-ttu-id="9bac6-341">К встроенным атрибутам проверки относятся:</span><span class="sxs-lookup"><span data-stu-id="9bac6-341">Built-in validation attributes include:</span></span>

* <span data-ttu-id="9bac6-342">`[CreditCard]`: Проверяет, что свойство имеет формат кредитной карты.</span><span class="sxs-lookup"><span data-stu-id="9bac6-342">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="9bac6-343">`[Compare]`: Проверяет соответствие двух свойств в модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-343">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="9bac6-344">Например, файл *Register.cshtml.cs* использует `[Compare]` для проверки совпадений двух введенных паролей.</span><span class="sxs-lookup"><span data-stu-id="9bac6-344">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="9bac6-345">[Удостоверение шаблона](xref:security/authentication/scaffold-identity) для просмотра кода регистрации.</span><span class="sxs-lookup"><span data-stu-id="9bac6-345">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="9bac6-346">`[EmailAddress]`: Проверяет, что свойство имеет формат электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9bac6-346">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="9bac6-347">`[Phone]`: Проверяет, что свойство имеет формат номера телефона.</span><span class="sxs-lookup"><span data-stu-id="9bac6-347">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="9bac6-348">`[Range]`: Проверяет, попадает значение свойства в указанный диапазон.</span><span class="sxs-lookup"><span data-stu-id="9bac6-348">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="9bac6-349">`[RegularExpression]`: Проверяет соответствие значения свойства указанному регулярному выражению.</span><span class="sxs-lookup"><span data-stu-id="9bac6-349">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="9bac6-350">`[Required]`: Проверяет, что поле не имеет значение null.</span><span class="sxs-lookup"><span data-stu-id="9bac6-350">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="9bac6-351">Дополнительные сведения о поведении этого атрибута см. в разделе [ `[Required]` Attribute](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="9bac6-351">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="9bac6-352">`[StringLength]`: Проверяет, что значение свойства строки не превышает заданное ограничение длины.</span><span class="sxs-lookup"><span data-stu-id="9bac6-352">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="9bac6-353">`[Url]`: Проверяет, что свойство имеет формат URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="9bac6-353">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="9bac6-354">`[Remote]`: Проверяет входные данные на клиенте, вызывая метод действия на сервере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-354">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="9bac6-355">Дополнительные сведения о поведении этого атрибута см. в разделе [ `[Remote]` Attribute](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="9bac6-355">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="9bac6-356">При использовании атрибута `[RegularExpression]` с проверкой на стороне клиента регулярное выражение выполняется в JavaScript в клиенте.</span><span class="sxs-lookup"><span data-stu-id="9bac6-356">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="9bac6-357">Это означает, что будет использоваться поведение сопоставления [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior).</span><span class="sxs-lookup"><span data-stu-id="9bac6-357">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="9bac6-358">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/corefx/issues/42487).</span><span class="sxs-lookup"><span data-stu-id="9bac6-358">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="9bac6-359">Полный перечень атрибутов проверки можно найти в пространстве имен [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="9bac6-359">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="9bac6-360">Сообщения об ошибках</span><span class="sxs-lookup"><span data-stu-id="9bac6-360">Error messages</span></span>

<span data-ttu-id="9bac6-361">Атрибуты проверки позволяют указать сообщение об ошибке, которое будет отображаться, если входные данные недопустимы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-361">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="9bac6-362">Пример:</span><span class="sxs-lookup"><span data-stu-id="9bac6-362">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="9bac6-363">На внутреннем уровне атрибуты вызывают `String.Format` с заполнителем для имени поля и иногда дополнительным заполнителями.</span><span class="sxs-lookup"><span data-stu-id="9bac6-363">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="9bac6-364">Пример:</span><span class="sxs-lookup"><span data-stu-id="9bac6-364">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="9bac6-365">При применении к свойству `Name` сообщение об ошибке, созданное в приведенном выше коде, имело бы вид Name length must be between 6 and 8 (Длина имени должна быть от 6 до 8).</span><span class="sxs-lookup"><span data-stu-id="9bac6-365">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="9bac6-366">Чтобы узнать, какие параметры передаются в `String.Format` для сообщения об ошибке определенного атрибута, см. раздел [Исходный код DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="9bac6-366">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="9bac6-367">Атрибут [Required]</span><span class="sxs-lookup"><span data-stu-id="9bac6-367">[Required] attribute</span></span>

<span data-ttu-id="9bac6-368">По умолчанию система проверки рассматривает не допускающие значение NULL параметры или свойства так, как если бы они имели атрибут `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-368">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="9bac6-369">[Типы значений](/dotnet/csharp/language-reference/keywords/value-types), например `decimal` и `int`, не поддерживают значение NULL.</span><span class="sxs-lookup"><span data-stu-id="9bac6-369">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="9bac6-370">Проверка [Required] на сервере</span><span class="sxs-lookup"><span data-stu-id="9bac6-370">[Required] validation on the server</span></span>

<span data-ttu-id="9bac6-371">На сервере обязательное значение считается отсутствующим, если свойство имеет значение NULL.</span><span class="sxs-lookup"><span data-stu-id="9bac6-371">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="9bac6-372">Поле, не допускающее значения NULL, всегда является допустимым, и сообщение об ошибке атрибута [Required] никогда не выводится.</span><span class="sxs-lookup"><span data-stu-id="9bac6-372">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="9bac6-373">Тем не менее привязка модели для ненулевого свойства может завершиться ошибкой, приводящей к сообщению об ошибке, например `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-373">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="9bac6-374">Чтобы задать настраиваемое сообщение об ошибке во время проверки не допускающих значения NULL типов на стороне сервера, у вас есть следующие варианты.</span><span class="sxs-lookup"><span data-stu-id="9bac6-374">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="9bac6-375">Сделать поле допускающим значение NULL (например, `decimal?` вместо `decimal`).</span><span class="sxs-lookup"><span data-stu-id="9bac6-375">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="9bac6-376">Типы значений [Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) обрабатываются как стандартные типы, допускающие значение NULL.</span><span class="sxs-lookup"><span data-stu-id="9bac6-376">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="9bac6-377">Указать сообщение об ошибке по умолчанию для использования в привязке модели, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-377">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="9bac6-378">Дополнительные сведения об ошибках привязки модели, у которых можно задать сообщение по умолчанию, см. в разделе <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="9bac6-378">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="9bac6-379">Проверка [Required] на клиенте</span><span class="sxs-lookup"><span data-stu-id="9bac6-379">[Required] validation on the client</span></span>

<span data-ttu-id="9bac6-380">Типы и строки, не допускающие значение NULL, обрабатываются на клиенте не так, как на сервере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-380">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="9bac6-381">На клиенте:</span><span class="sxs-lookup"><span data-stu-id="9bac6-381">On the client:</span></span>

* <span data-ttu-id="9bac6-382">Значение считается присутствующим только в том случае, если для него вводятся данные.</span><span class="sxs-lookup"><span data-stu-id="9bac6-382">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="9bac6-383">Таким образом, проверка на стороне клиента обрабатывает типы, не допускающие значение NULL, так же, как обнуляемые типы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-383">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="9bac6-384">Пробелы в поле строки считаются допустимыми входными данными при проверке методом jQuery [required](https://jqueryvalidation.org/required-method/).</span><span class="sxs-lookup"><span data-stu-id="9bac6-384">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="9bac6-385">Проверка на стороне сервера считает, что обязательное строковое поле недопустимо, если введены только пробелы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-385">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="9bac6-386">Как отмечалось ранее, не допускающие значение NULL типы рассматриваются как имеющие атрибут `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-386">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="9bac6-387">Это означает, что вы получаете проверку на стороне клиента, даже если не применять атрибут `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-387">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="9bac6-388">Но если вы не используете атрибут, вы получаете сообщение об ошибке по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="9bac6-388">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="9bac6-389">Чтобы задать настраиваемое сообщение об ошибке, используйте атрибут.</span><span class="sxs-lookup"><span data-stu-id="9bac6-389">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="9bac6-390">Атрибут [Remote]</span><span class="sxs-lookup"><span data-stu-id="9bac6-390">[Remote] attribute</span></span>

<span data-ttu-id="9bac6-391">Атрибут `[Remote]` реализует проверку на стороне клиента, в ходе которой требуется вызвать метод на сервере, чтобы определить, является ли допустимым поле ввода.</span><span class="sxs-lookup"><span data-stu-id="9bac6-391">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="9bac6-392">Например, приложению может потребоваться проверить, занято ли имя пользователя.</span><span class="sxs-lookup"><span data-stu-id="9bac6-392">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="9bac6-393">Для реализации удаленной проверки сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="9bac6-393">To implement remote validation:</span></span>

1. <span data-ttu-id="9bac6-394">Создайте для вызова из JavaScript метод действия.</span><span class="sxs-lookup"><span data-stu-id="9bac6-394">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="9bac6-395">Метод проверки jQuery [remote](https://jqueryvalidation.org/remote-method/) ожидает ответ JSON.</span><span class="sxs-lookup"><span data-stu-id="9bac6-395">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="9bac6-396">`"true"` означает, что входные данные допустимы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-396">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="9bac6-397">`"false"`, `undefined` или `null` означают, что входные данные недопустимы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-397">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="9bac6-398">Вывод стандартного сообщения об ошибке</span><span class="sxs-lookup"><span data-stu-id="9bac6-398">Display the default error message.</span></span>
   * <span data-ttu-id="9bac6-399">Все прочие значения означают, что входные данные недопустимы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-399">Any other string means the input is invalid.</span></span> <span data-ttu-id="9bac6-400">Вывод строки как настраиваемого сообщения об ошибке.</span><span class="sxs-lookup"><span data-stu-id="9bac6-400">Display the string as a custom error message.</span></span>

   <span data-ttu-id="9bac6-401">Ниже приведен пример метода действия, который возвращает настраиваемое сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="9bac6-401">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="9bac6-402">В классе модели пометьте свойство атрибутом `[Remote]`, указывающим метод действия проверки, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-402">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="9bac6-403">Атрибут `[Remote]` находится в пространстве имен `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-403">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="9bac6-404">Установите пакет NuGet [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures), если вы не используете метапакет `Microsoft.AspNetCore.App` или `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-404">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="9bac6-405">Дополнительные поля</span><span class="sxs-lookup"><span data-stu-id="9bac6-405">Additional fields</span></span>

<span data-ttu-id="9bac6-406">Свойство `AdditionalFields` атрибута `[Remote]` позволяет проверять сочетания полей с данными на сервере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-406">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="9bac6-407">Например, если бы в модели `User` были свойства `FirstName` и `LastName`, могла бы возникнуть необходимость проверить, нет ли уже пользователя с такой парой имен.</span><span class="sxs-lookup"><span data-stu-id="9bac6-407">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="9bac6-408">В следующем примере показано использование `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-408">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="9bac6-409">`AdditionalFields` можно явно присвоить строкам `"FirstName"` и `"LastName"`, но использование оператора [nameof](/dotnet/csharp/language-reference/keywords/nameof) упрощает дальнейший рефакторинг.</span><span class="sxs-lookup"><span data-stu-id="9bac6-409">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="9bac6-410">Методу действия для этой проверки необходимо принимать аргументы для имени и фамилии.</span><span class="sxs-lookup"><span data-stu-id="9bac6-410">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="9bac6-411">Когда пользователь вводит имя или фамилию, JavaScript выполняет удаленный вызов, чтобы увидеть, будет ли эта пара принята.</span><span class="sxs-lookup"><span data-stu-id="9bac6-411">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="9bac6-412">Чтобы проверить несколько дополнительных полей, их следует указывать в виде списка с разделителями-запятыми.</span><span class="sxs-lookup"><span data-stu-id="9bac6-412">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="9bac6-413">Например, чтобы добавить в модель свойство `MiddleName`, задайте атрибут `[Remote]`, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-413">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="9bac6-414">`AdditionalFields`, как и все аргументы атрибутов, должен представлять собой константное выражение.</span><span class="sxs-lookup"><span data-stu-id="9bac6-414">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="9bac6-415">Поэтому не следует использовать [интерполированную строку](/dotnet/csharp/language-reference/keywords/interpolated-strings) или вызов <xref:System.String.Join*>для инициализации `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-415">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="9bac6-416">Альтернативы для встроенных атрибутов</span><span class="sxs-lookup"><span data-stu-id="9bac6-416">Alternatives to built-in attributes</span></span>

<span data-ttu-id="9bac6-417">Если вам нужна проверка, которую не предоставляют встроенные атрибуты, вы можете следующее.</span><span class="sxs-lookup"><span data-stu-id="9bac6-417">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="9bac6-418">[Создать настраиваемые атрибуты](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="9bac6-418">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="9bac6-419">[Реализовать IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="9bac6-419">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="9bac6-420">Настраиваемые атрибуты</span><span class="sxs-lookup"><span data-stu-id="9bac6-420">Custom attributes</span></span>

<span data-ttu-id="9bac6-421">Для сценариев, где не годятся встроенные атрибуты проверки, можно создать настраиваемые атрибуты.</span><span class="sxs-lookup"><span data-stu-id="9bac6-421">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="9bac6-422">Создайте класс, наследуемый от <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, и переопределите метод <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="9bac6-422">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="9bac6-423">Метод `IsValid` принимает объект с именем *value*, который является входными данными для проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-423">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="9bac6-424">Перегрузка также принимает объект `ValidationContext`, который предоставляет дополнительные сведения, такие как экземпляр модели, созданный с помощью привязки модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-424">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="9bac6-425">В следующем примере проверяется, что дата выпуска фильмов в *классическом* жанре задана не позднее указанного года.</span><span class="sxs-lookup"><span data-stu-id="9bac6-425">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="9bac6-426">Атрибут `[ClassicMovie2]` сначала проверяет жанр и продолжает проверку, только если он *классический*.</span><span class="sxs-lookup"><span data-stu-id="9bac6-426">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="9bac6-427">У фильмов, попавших в классику, система проверяет даты выпуска, чтобы убедиться в том, что она не является более поздней, чем ограничение, переданное в конструктор атрибута.</span><span class="sxs-lookup"><span data-stu-id="9bac6-427">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="9bac6-428">Приведенная выше переменная `movie` представляет объект `Movie`, который содержит данные из переданной формы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-428">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="9bac6-429">Метод `IsValid` проверяет дату и жанр.</span><span class="sxs-lookup"><span data-stu-id="9bac6-429">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="9bac6-430">После успешной проверки `IsValid` возвращает код `ValidationResult.Success`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-430">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="9bac6-431">Если проверка завершается неудачно, возвращается `ValidationResult` с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="9bac6-431">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="9bac6-432">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="9bac6-432">IValidatableObject</span></span>

<span data-ttu-id="9bac6-433">Предыдущий пример работает только с типами `Movie`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-433">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="9bac6-434">Другой вариант для проверки на уровне класса — реализация `IValidatableObject` в классе модели, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-434">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="9bac6-435">Проверка узлов верхнего уровня</span><span class="sxs-lookup"><span data-stu-id="9bac6-435">Top-level node validation</span></span>

<span data-ttu-id="9bac6-436">Узлы верхнего уровня содержат:</span><span class="sxs-lookup"><span data-stu-id="9bac6-436">Top-level nodes include:</span></span>

* <span data-ttu-id="9bac6-437">параметры действия;</span><span class="sxs-lookup"><span data-stu-id="9bac6-437">Action parameters</span></span>
* <span data-ttu-id="9bac6-438">свойства контроллера;</span><span class="sxs-lookup"><span data-stu-id="9bac6-438">Controller properties</span></span>
* <span data-ttu-id="9bac6-439">параметры обработчика страниц;</span><span class="sxs-lookup"><span data-stu-id="9bac6-439">Page handler parameters</span></span>
* <span data-ttu-id="9bac6-440">свойства страничной модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-440">Page model properties</span></span>

<span data-ttu-id="9bac6-441">Проверка привязанных к модели узлов верхнего уровня осуществляется наряду с проверкой свойств модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-441">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="9bac6-442">В следующем примере, взятом из примера приложения, метод `VerifyPhone` использует класс <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> для проверки параметра действия `phone`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-442">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="9bac6-443">Узлы верхнего уровня могут применять класс <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> с атрибутами проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-443">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="9bac6-444">В следующем примере, взятом из примера приложения, метод `CheckAge` указывает, что при отправке формы параметр `age` должен быть привязан из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="9bac6-444">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="9bac6-445">На странице "Check Age" (Проверка возраста) (*CheckAge.cshtml*) находятся две формы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-445">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="9bac6-446">Первая форма отправляет значение `Age`, равное `99`, в виде строки запроса: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-446">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="9bac6-447">Если из строки запроса отправлен параметр `age` в правильном формате, форма проходит проверку.</span><span class="sxs-lookup"><span data-stu-id="9bac6-447">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="9bac6-448">Вторая форма на странице "Check Age" (Проверка возраста) отправляет значение `Age` в теле запроса, и проверка завершается сбоем.</span><span class="sxs-lookup"><span data-stu-id="9bac6-448">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="9bac6-449">Ошибка привязки связана с тем, что параметр `age` должен поступать из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="9bac6-449">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="9bac6-450">При работе с `CompatibilityVersion.Version_2_1` или более поздней версией проверка узла верхнего уровня по умолчанию включена.</span><span class="sxs-lookup"><span data-stu-id="9bac6-450">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="9bac6-451">В противном случае проверка узла верхнего уровня отключена.</span><span class="sxs-lookup"><span data-stu-id="9bac6-451">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="9bac6-452">Параметр по умолчанию можно переопределить, задав свойство <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> в (`Startup.ConfigureServices`), как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="9bac6-452">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="9bac6-453">Максимальное количество ошибок</span><span class="sxs-lookup"><span data-stu-id="9bac6-453">Maximum errors</span></span>

<span data-ttu-id="9bac6-454">При достижении максимального количества ошибок (по умолчанию 200) проверка прекращается.</span><span class="sxs-lookup"><span data-stu-id="9bac6-454">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="9bac6-455">Это число можно изменить с помощью следующего кода в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9bac6-455">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="9bac6-456">Максимальная рекурсия</span><span class="sxs-lookup"><span data-stu-id="9bac6-456">Maximum recursion</span></span>

<span data-ttu-id="9bac6-457"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> проходит через граф объектов в проверяемой модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-457"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="9bac6-458">У моделей, которые очень глубоки или содержат бесконечную рекурсию, в ходе проверки может произойти переполнение стека.</span><span class="sxs-lookup"><span data-stu-id="9bac6-458">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="9bac6-459">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) предоставляет способ остановить проверку до превышения настроенной глубины рекурсии обхода.</span><span class="sxs-lookup"><span data-stu-id="9bac6-459">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="9bac6-460">Значение `MvcOptions.MaxValidationDepth` по умолчанию — 32 при работе в `CompatibilityVersion.Version_2_2` или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="9bac6-460">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="9bac6-461">Для более ранних версий значение равно NULL; это означает отсутствие ограничения глубины.</span><span class="sxs-lookup"><span data-stu-id="9bac6-461">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="9bac6-462">Автоматическое сокращение</span><span class="sxs-lookup"><span data-stu-id="9bac6-462">Automatic short-circuit</span></span>

<span data-ttu-id="9bac6-463">Проверка автоматически сокращается (пропускается), если граф модели не требует проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-463">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="9bac6-464">К числу объектов, которые среда выполнения пропускает при проверке, относятся коллекции примитивов (такие как `byte[]`, `string[]`, `Dictionary<string, string>`) и сложные графы объектов, которые не имеют проверяющих элементов управления.</span><span class="sxs-lookup"><span data-stu-id="9bac6-464">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="9bac6-465">Отключение проверки</span><span class="sxs-lookup"><span data-stu-id="9bac6-465">Disable validation</span></span>

<span data-ttu-id="9bac6-466">Чтобы отключить проверку, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="9bac6-466">To disable validation:</span></span>

1. <span data-ttu-id="9bac6-467">Создайте реализацию интерфейса `IObjectModelValidator`, которая не помечает поля как недопустимые.</span><span class="sxs-lookup"><span data-stu-id="9bac6-467">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="9bac6-468">Добавьте следующий код в `Startup.ConfigureServices` для замены реализации `IObjectModelValidator` по умолчанию в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="9bac6-468">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="9bac6-469">По-прежнему могут отображаться ошибки состояния модели, которые идут из привязки модели.</span><span class="sxs-lookup"><span data-stu-id="9bac6-469">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="9bac6-470">Проверка на стороне клиента</span><span class="sxs-lookup"><span data-stu-id="9bac6-470">Client-side validation</span></span>

<span data-ttu-id="9bac6-471">Проверка на стороне клиента не позволяет отправлять форму, пока ее данные не будут допустимыми.</span><span class="sxs-lookup"><span data-stu-id="9bac6-471">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="9bac6-472">При нажатии кнопки "Отправить" выполняется код JavaScript, который либо отправляет форму, либо выводит сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="9bac6-472">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="9bac6-473">Проверка на стороне клиента позволяет избежать ненужного кругового захода на сервер при наличии ошибки ввода в форме.</span><span class="sxs-lookup"><span data-stu-id="9bac6-473">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="9bac6-474">Ссылки на следующий скрипт в *_Layout.cshtml* и *_ValidationScriptsPartial.cshtml* поддерживают проверку на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="9bac6-474">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="9bac6-475">Скрипт [ненавязчивой проверки jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) — это настраиваемая интерфейсная библиотека Майкрософт, которая основана на популярном подключаемом модуле [jQuery Validate](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="9bac6-475">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="9bac6-476">Без скрипта ненавязчивой проверки jQuery одну и ту же логику проверки приходилось бы реализовывать в двух местах: в атрибутах проверки для свойств модели на стороне сервера, а затем еще раз в скриптах на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="9bac6-476">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="9bac6-477">Вместо этого [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и [вспомогательные функции HTML](xref:mvc/views/overview) могут использовать атрибуты проверки и метаданные типов из свойств модели для обработки атрибутов `data-` HTML 5 в элементах форм, требующих проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-477">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="9bac6-478">Скрипт ненавязчивой проверки jQuery анализирует эти атрибуты `data-` и передает логику в подключаемый модуль jQuery Validate, по сути копируя логику проверки на стороне сервера в клиент.</span><span class="sxs-lookup"><span data-stu-id="9bac6-478">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="9bac6-479">Ошибки проверки могут выводиться в клиенте с помощью вспомогательных функций тегов, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="9bac6-479">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="9bac6-480">Приведенные выше вспомогательные функции тегов отрисовывают следующий код HTML.</span><span class="sxs-lookup"><span data-stu-id="9bac6-480">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="9bac6-481">Обратите внимание на то, что атрибуты `data-` в выходных данных HTML соответствуют атрибутам проверки для свойства `ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-481">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="9bac6-482">Атрибут `data-val-required` содержит сообщение об ошибке, которое выводится, если пользователь не заполнил поле даты выхода.</span><span class="sxs-lookup"><span data-stu-id="9bac6-482">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="9bac6-483">Скрипт ненавязчивой проверки jQuery передает это значение в метод [required()](https://jqueryvalidation.org/required-method/) подключаемого модуля jQuery Validate, который затем выводит это сообщение в соответствующем элементе **\<span>**.</span><span class="sxs-lookup"><span data-stu-id="9bac6-483">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="9bac6-484">Проверка типа данных основана на типе свойства в .NET, если его не переопределяет атрибут `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-484">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="9bac6-485">Браузеры имеют свои сообщения об по умолчанию, но пакет ненавязчивой проверки jQuery может переопределять эти сообщения.</span><span class="sxs-lookup"><span data-stu-id="9bac6-485">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="9bac6-486">Атрибуты и подклассы `[DataType]`, такие как `[EmailAddress]`, позволяют указать сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="9bac6-486">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="9bac6-487">Добавление проверки к динамическим формам</span><span class="sxs-lookup"><span data-stu-id="9bac6-487">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="9bac6-488">Скрипт ненавязчивой проверки jQuery передает логику и параметры проверки в подключаемый модуль jQuery Validate при первой загрузке страницы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-488">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="9bac6-489">Поэтому динамически создаваемые формы не подвергаются проверке автоматически.</span><span class="sxs-lookup"><span data-stu-id="9bac6-489">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="9bac6-490">Чтобы включить проверку, необходимо указать, что скрипт ненавязчивой проверки jQuery должен анализировать динамическую форму сразу после ее создания.</span><span class="sxs-lookup"><span data-stu-id="9bac6-490">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="9bac6-491">Например, приведенный ниже код показывает, как можно настроить проверку на стороне клиента для формы, добавленной посредством AJAX.</span><span class="sxs-lookup"><span data-stu-id="9bac6-491">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="9bac6-492">Метод `$.validator.unobtrusive.parse()` принимает селектор jQuery в качестве единственного аргумента.</span><span class="sxs-lookup"><span data-stu-id="9bac6-492">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="9bac6-493">Этот метод предписывает скрипту ненавязчивой проверки jQuery анализировать атрибуты `data-` форм в этом селекторе.</span><span class="sxs-lookup"><span data-stu-id="9bac6-493">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="9bac6-494">Значения этих атрибутов затем передаются в подключаемый модуль jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="9bac6-494">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="9bac6-495">Добавление проверки к динамическим элементам управления</span><span class="sxs-lookup"><span data-stu-id="9bac6-495">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="9bac6-496">Метод `$.validator.unobtrusive.parse()` обрабатывает всю форму, а не отдельные динамически создаваемые элементы управления, такие как `<input>` и `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-496">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="9bac6-497">Для повторной обработки формы удалите данные проверки, которые были добавлены при анализе формы ранее, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="9bac6-497">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="9bac6-498">Настраиваемая проверка на стороне клиента</span><span class="sxs-lookup"><span data-stu-id="9bac6-498">Custom client-side validation</span></span>

<span data-ttu-id="9bac6-499">Настраиваемая проверка на стороне клиента выполняется путем создания атрибутов HTML `data-`, которые работают с настраиваемым адаптером проверки jQuery.</span><span class="sxs-lookup"><span data-stu-id="9bac6-499">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="9bac6-500">В следующем примере кода адаптера используются атрибуты `ClassicMovie` и `ClassicMovie2`, которые были введены ранее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="9bac6-500">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="9bac6-501">Сведения о способах создания адаптеров см. в [документации по проверкам jQuery](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="9bac6-501">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="9bac6-502">Использование адаптера для заданного поля инициируется атрибутами `data-`, которые:</span><span class="sxs-lookup"><span data-stu-id="9bac6-502">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="9bac6-503">помечают поле как проходящее проверку (`data-val="true"`);</span><span class="sxs-lookup"><span data-stu-id="9bac6-503">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="9bac6-504">указывают имя правила проверки и текст сообщения об ошибке (например, `data-val-rulename="Error message."`);</span><span class="sxs-lookup"><span data-stu-id="9bac6-504">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="9bac6-505">указывают любые дополнительные параметры, в которых нуждается проверяющий элемент управления (например, `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="9bac6-505">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="9bac6-506">В следующем примере показаны атрибуты `data-` для нашего атрибута `ClassicMovie` из примера приложения.</span><span class="sxs-lookup"><span data-stu-id="9bac6-506">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="9bac6-507">Как отмечалось ранее, [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и [вспомогательные методы HTML](xref:mvc/views/overview) используют сведения из атрибутов проверки для подготовки к отрисовке атрибутов `data-`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-507">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="9bac6-508">Существует два варианта для написания кода, который приводит к созданию настраиваемых атрибутов HTML `data-`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-508">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="9bac6-509">Создайте класс, производный от `AttributeAdapterBase<TAttribute>`, и класс, реализующий `IValidationAttributeAdapterProvider`; зарегистрируйте атрибут и его адаптер в функции внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="9bac6-509">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="9bac6-510">Этот метод следует за [участником с единственной обязанностью](https://wikipedia.org/wiki/Single_responsibility_principle) в том, что код проверки, связанный с сервером и клиентом, выделен в отдельные классы.</span><span class="sxs-lookup"><span data-stu-id="9bac6-510">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="9bac6-511">Адаптер также имеет следующее преимущество: так как он зарегистрирован в функции внедрения зависимостей, для него при необходимости доступны другие службы в ней.</span><span class="sxs-lookup"><span data-stu-id="9bac6-511">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="9bac6-512">Реализуйте `IClientModelValidator` в вашем классе `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-512">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="9bac6-513">Этот метод стоит использовать, если атрибут не выполняет проверку на стороне сервера и не нуждается в службах из функции внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="9bac6-513">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="9bac6-514">AttributeAdapter для проверки на стороне клиента</span><span class="sxs-lookup"><span data-stu-id="9bac6-514">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="9bac6-515">Этот метод отрисовки атрибутов `data-` в формате HTML используется атрибутом `ClassicMovie` в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="9bac6-515">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="9bac6-516">Чтобы добавить клиентскую проверку с помощью этого метода, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="9bac6-516">To add client validation by using this method:</span></span>

1. <span data-ttu-id="9bac6-517">Создайте класс адаптера атрибута для настраиваемого атрибута проверки.</span><span class="sxs-lookup"><span data-stu-id="9bac6-517">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="9bac6-518">Наследуйте класс от [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="9bac6-518">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="9bac6-519">Создайте метод `AddValidation`, который добавляет атрибуты `data-` для выводимых данных, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-519">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="9bac6-520">Создайте класс поставщика адаптера, который реализует <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="9bac6-520">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="9bac6-521">В методе `GetAttributeAdapter` передайте настраиваемый атрибут в конструктор адаптера, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-521">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="9bac6-522">Зарегистрируйте поставщик адаптера для внедрения зависимостей в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-522">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="9bac6-523">IClientModelValidator для проверки на стороне клиента</span><span class="sxs-lookup"><span data-stu-id="9bac6-523">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="9bac6-524">Этот метод отрисовки атрибутов `data-` в формате HTML используется атрибутом `ClassicMovie2` в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="9bac6-524">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="9bac6-525">Чтобы добавить клиентскую проверку с помощью этого метода, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="9bac6-525">To add client validation by using this method:</span></span>

* <span data-ttu-id="9bac6-526">В настраиваемом атрибуте проверки реализуйте интерфейс `IClientModelValidator` и создайте метод `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="9bac6-526">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="9bac6-527">В метод `AddValidation` добавьте атрибуты `data-` для проверки, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9bac6-527">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="9bac6-528">Отключение проверки на стороне клиента</span><span class="sxs-lookup"><span data-stu-id="9bac6-528">Disable client-side validation</span></span>

<span data-ttu-id="9bac6-529">Следующий код отключает клиентскую проверку в представлениях MVC.</span><span class="sxs-lookup"><span data-stu-id="9bac6-529">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="9bac6-530">В Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="9bac6-530">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="9bac6-531">Еще один вариант отключения клиентской проверки клиента — закомментировать ссылку на `_ValidationScriptsPartial` в вашем файле *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9bac6-531">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9bac6-532">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9bac6-532">Additional resources</span></span>

* [<span data-ttu-id="9bac6-533">Пространство имен System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="9bac6-533">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="9bac6-534">Привязка модели</span><span class="sxs-lookup"><span data-stu-id="9bac6-534">Model Binding</span></span>](model-binding.md)

::: moniker-end
