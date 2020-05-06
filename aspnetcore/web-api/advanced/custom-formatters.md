---
title: Пользовательские модули форматирования для веб-API в ASP.NET Core
author: rick-anderson
description: Узнайте, как создавать и использовать пользовательские модули форматирования для веб-интерфейсов API в ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 0836fc288a015adb9a6223c5a2b681b1b03bded4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777323"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="8a0bb-103">Пользовательские модули форматирования для веб-API в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a0bb-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="8a0bb-104">Автор: [Том Дикстра](https://github.com/tdykstra) (Tom Dykstra)</span><span class="sxs-lookup"><span data-stu-id="8a0bb-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="8a0bb-105">ASP.NET Core MVC поддерживает обмен данными в веб-API с помощью форматировщиков ввода и вывода.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="8a0bb-106">Форматировщики ввода используются [привязкой модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="8a0bb-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="8a0bb-107">Модули форматирования выходных данных используются для [форматирования ответов](xref:web-api/advanced/formatting).</span><span class="sxs-lookup"><span data-stu-id="8a0bb-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="8a0bb-108">Платформа предоставляет встроенные форматировщики ввода и вывода для JSON и XML.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="8a0bb-109">Доступен только встроенный форматировщик вывода для обычного текста, но не форматировщик ввода для обычного текста.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="8a0bb-110">В этой статье показано, как добавить поддержку дополнительных форматов, создав пользовательские модули форматирования.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="8a0bb-111">Пример пользовательского форматировщика ввода для обычного текста см. в описании [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) на GitHub.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-111">For an example of a custom input formatter for plain text, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="8a0bb-112">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a0bb-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="8a0bb-113">Когда следует использовать пользовательские модули форматирования</span><span class="sxs-lookup"><span data-stu-id="8a0bb-113">When to use custom formatters</span></span>

<span data-ttu-id="8a0bb-114">Используйте пользовательский форматировщик, если необходимо, чтобы процесс [согласования содержимого](xref:web-api/advanced/formatting#content-negotiation) поддерживал тип содержимого, который не поддерживается встроенными форматировщиками.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-114">Use a custom formatter when you want the [content negotiation](xref:web-api/advanced/formatting#content-negotiation) process to support a content type that isn't supported by the built-in formatters.</span></span>

<span data-ttu-id="8a0bb-115">Например, если некоторые из клиентов вашего веб-интерфейса API могут работать с форматом [Protobuf](https://github.com/google/protobuf), для обмена данными с ними может быть желательно использовать этот формат, так как он более эффективен.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-115">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span> <span data-ttu-id="8a0bb-116">Вам может также потребоваться реализовать отправку имен и адресов веб-интерфейсом API в формате [vCard](https://wikipedia.org/wiki/VCard), распространенном формате для передачи контактных данных.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-116">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="8a0bb-117">В образце приложения, представленном в этой статье, реализуется простой модуль форматирования vCard.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-117">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="8a0bb-118">Общие сведения об использовании пользовательского модуля форматирования</span><span class="sxs-lookup"><span data-stu-id="8a0bb-118">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="8a0bb-119">Чтобы создать и использовать пользовательский модуль форматирования, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-119">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="8a0bb-120">Создайте класс модуля форматирования выходных данных, если необходимо сериализовать данные, отправляемые клиенту.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-120">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="8a0bb-121">Создайте класс модуля форматирования входных данных, если необходимо десериализировать данные, получаемые от клиента.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-121">Create an input formatter class if you want to deserialize data received from the client.</span></span>
* <span data-ttu-id="8a0bb-122">Добавьте экземпляры модулей форматирования в коллекции `InputFormatters` и `OutputFormatters` в [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="8a0bb-122">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="8a0bb-123">В следующих разделах приводятся инструкции по выполнению каждого из этих действий с примерами кода.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-123">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="8a0bb-124">Создание пользовательского класса модуля форматирования</span><span class="sxs-lookup"><span data-stu-id="8a0bb-124">How to create a custom formatter class</span></span>

<span data-ttu-id="8a0bb-125">Чтобы создать модуль форматирования, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-125">To create a formatter:</span></span>

* <span data-ttu-id="8a0bb-126">Наследуйте класс от подходящего базового класса.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-126">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="8a0bb-127">Укажите в конструкторе допустимые типы передаваемых данных и кодировки.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-127">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="8a0bb-128">Переопределите методы `CanReadType`/`CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-128">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="8a0bb-129">Переопределите методы `ReadRequestBodyAsync`/`WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-129">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="8a0bb-130">Наследование от подходящего базового класса</span><span class="sxs-lookup"><span data-stu-id="8a0bb-130">Derive from the appropriate base class</span></span>

<span data-ttu-id="8a0bb-131">Для текстовых типов данных (например, vCard) произведите наследование от базового класса [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) или [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).</span><span class="sxs-lookup"><span data-stu-id="8a0bb-131">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="8a0bb-132">Пример форматировщика входных данных см. в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="8a0bb-132">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

<span data-ttu-id="8a0bb-133">Для двоичных типов произведите наследование от базового класса [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) или [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter).</span><span class="sxs-lookup"><span data-stu-id="8a0bb-133">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="8a0bb-134">Указание допустимых типов передаваемых данных и кодировок.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-134">Specify valid media types and encodings</span></span>

<span data-ttu-id="8a0bb-135">Укажите в конструкторе допустимые типы передаваемых данных и кодировки, добавив их в коллекции `SupportedMediaTypes` и `SupportedEncodings`.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-135">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

<span data-ttu-id="8a0bb-136">Пример форматировщика входных данных см. в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="8a0bb-136">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="8a0bb-137">Внедрение зависимостей конструктора в класс модуля форматирования невозможно.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-137">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="8a0bb-138">Например, невозможно получить средство ведения журнала, добавив соответствующий параметр в конструктор.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-138">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="8a0bb-139">Для доступа к службам необходимо использовать объект контекста, который передается в ваши методы.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-139">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="8a0bb-140">В приведенном [ниже](#read-write) примере кода показано, как это делается.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-140">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="8a0bb-141">Переопределение методов CanReadType и CanWriteType</span><span class="sxs-lookup"><span data-stu-id="8a0bb-141">Override CanReadType/CanWriteType</span></span>

<span data-ttu-id="8a0bb-142">Укажите типы, в которые может осуществляться десериализация или из которых может осуществляться сериализация, переопределив метод `CanReadType` или `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-142">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="8a0bb-143">Например, текст в формате vCard может создаваться только из типа `Contact`, и наоборот.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-143">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

<span data-ttu-id="8a0bb-144">Пример форматировщика входных данных см. в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="8a0bb-144">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="8a0bb-145">Метод CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="8a0bb-145">The CanWriteResult method</span></span>

<span data-ttu-id="8a0bb-146">В некоторых ситуациях следует переопределять метод `CanWriteResult`, а не `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-146">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="8a0bb-147">Используйте `CanWriteResult`, если выполняются указанные ниже условия.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-147">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="8a0bb-148">Метод действия возвращает класс модели.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-148">Your action method returns a model class.</span></span>
* <span data-ttu-id="8a0bb-149">Существуют производные классы, которые могут возвращаться во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-149">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="8a0bb-150">Во время выполнения необходимо знать, какой производный класс был возвращен действием.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-150">You need to know at runtime which derived class was returned by the action.</span></span>

<span data-ttu-id="8a0bb-151">Предположим, сигнатура метода действия возвращает тип `Person`, но может также возвращать типы `Student` и `Instructor`, производные от `Person`.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-151">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="8a0bb-152">Если модуль форматирования должен обрабатывать только объекты `Student`, проверьте тип свойства [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) в объекте контекста, предоставленном методу `CanWriteResult`.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-152">If you want your formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="8a0bb-153">Обратите внимание на то, что если метод действия возвращает `IActionResult`, использовать `CanWriteResult` нет необходимости. В этом случае метод `CanWriteType` получает тип во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-153">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="8a0bb-154">Переопределение методов ReadRequestBodyAsync и WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="8a0bb-154">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span>

<span data-ttu-id="8a0bb-155">Фактическая работа по десериализации и сериализации осуществляется в методе `ReadRequestBodyAsync` или `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-155">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="8a0bb-156">В выделенных строках ниже показано, как получить службы из контейнера внедрения зависимостей (получить их из параметров конструктора нельзя).</span><span class="sxs-lookup"><span data-stu-id="8a0bb-156">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

<span data-ttu-id="8a0bb-157">Пример форматировщика входных данных см. в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="8a0bb-157">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="8a0bb-158">Настройка использования пользовательского модуля форматирования в MVC</span><span class="sxs-lookup"><span data-stu-id="8a0bb-158">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="8a0bb-159">Для использования пользовательского модуля форматирования добавьте экземпляр его класса в коллекцию `InputFormatters` или `OutputFormatters`.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-159">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="8a0bb-160">Модули форматирования обрабатываются в порядке добавления.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-160">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="8a0bb-161">Первый модуль имеет приоритет.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-161">The first one takes precedence.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8a0bb-162">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="8a0bb-162">Next steps</span></span>

* <span data-ttu-id="8a0bb-163">[Пример приложения для этого документа](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), в котором реализуются простые форматировщики входных и выходных данных в формате vCard.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-163">[Sample app for this doc](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span> <span data-ttu-id="8a0bb-164">Это приложение считывает и записывает карточки vCard, которые выглядят следующим образом:</span><span class="sxs-lookup"><span data-stu-id="8a0bb-164">The apps reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR]
uid:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="8a0bb-165">Чтобы увидеть выходные данные vCard, запустите приложение и отправьте запрос Get с заголовком Accept "text/vcard" на адрес `http://localhost:63313/api/contacts/` (при запуске из Visual Studio) или `http://localhost:5000/api/contacts/` (при запуске из командной строки).</span><span class="sxs-lookup"><span data-stu-id="8a0bb-165">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="8a0bb-166">Чтобы добавить карточку vCard в коллекцию контактов в памяти, отправьте запрос Post на тот же URL-адрес. Запрос должен иметь заголовок Content-Type "text/vcard", а в его теле должен содержаться текст карточки vCard, отформатированный так, как показано выше.</span><span class="sxs-lookup"><span data-stu-id="8a0bb-166">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>
