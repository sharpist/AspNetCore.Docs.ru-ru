---
title: Пользовательская привязка модели в ASP.NET Core
author: ardalis
description: Узнайте, как привязка модели позволяет действиям контроллера работать непосредственно с типами модели в ASP.NET Core.
ms.author: riande
ms.date: 01/06/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 6e52d9c19a3d9c16f980021000db0bdecca5ed58
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018914"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="a8b85-103">Пользовательская привязка модели в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8b85-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a8b85-104">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="a8b85-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="a8b85-105">Привязка модели позволяет действиям контроллера работать непосредственно с типами моделей (передаваемыми в качестве аргументов метода), а не с HTTP-запросами.</span><span class="sxs-lookup"><span data-stu-id="a8b85-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="a8b85-106">Сопоставление между данными входящего запроса и моделями приложений обрабатывается связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="a8b85-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="a8b85-107">Разработчики могут расширить функциональность привязки встроенных модели путем реализации настраиваемых связывателей (хотя обычно создавать собственный поставщик не требуется).</span><span class="sxs-lookup"><span data-stu-id="a8b85-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="a8b85-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a8b85-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="a8b85-109">Ограничения для связывателя модели по умолчанию</span><span class="sxs-lookup"><span data-stu-id="a8b85-109">Default model binder limitations</span></span>

<span data-ttu-id="a8b85-110">Связыватели моделей по умолчанию поддерживают значительную часть распространенных типов данных .NET Core и должны соответствовать большинству требований разработчиков.</span><span class="sxs-lookup"><span data-stu-id="a8b85-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="a8b85-111">Ожидается, что они будут привязывать текстовые входные данные из запроса непосредственно к типам моделей.</span><span class="sxs-lookup"><span data-stu-id="a8b85-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="a8b85-112">Перед привязкой может потребоваться преобразовать входные данные.</span><span class="sxs-lookup"><span data-stu-id="a8b85-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="a8b85-113">Например, у вас есть ключ, используемый для поиска данных модели.</span><span class="sxs-lookup"><span data-stu-id="a8b85-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="a8b85-114">Для выборки данных на основе ключа можно воспользоваться настраиваемым связывателем модели.</span><span class="sxs-lookup"><span data-stu-id="a8b85-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="a8b85-115">Обзор привязки модели</span><span class="sxs-lookup"><span data-stu-id="a8b85-115">Model binding review</span></span>

<span data-ttu-id="a8b85-116">Для типов, с которыми работает привязка данных, используются специальные определения.</span><span class="sxs-lookup"><span data-stu-id="a8b85-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="a8b85-117">*Простой тип* преобразуется из одной строки во входных данных.</span><span class="sxs-lookup"><span data-stu-id="a8b85-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="a8b85-118">*Сложный тип* преобразуется из нескольких входных значений.</span><span class="sxs-lookup"><span data-stu-id="a8b85-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="a8b85-119">Платформа определяет их различие в зависимости от наличия `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="a8b85-120">Если у вас есть простое сопоставление `string` -> `SomeType`, не требующее внешних ресурсов, рекомендуется создать преобразователь типов.</span><span class="sxs-lookup"><span data-stu-id="a8b85-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="a8b85-121">Прежде чем создавать собственный настраиваемый связыватель модели, следует понять реализацию существующих связывателей моделей.</span><span class="sxs-lookup"><span data-stu-id="a8b85-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="a8b85-122">Рассмотрим класс <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, который используется для преобразования строк в кодировке Base64 в массивы байтов.</span><span class="sxs-lookup"><span data-stu-id="a8b85-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="a8b85-123">Массивы байтов часто хранятся в виде файлов или полей больших двоичных объектов базы данных.</span><span class="sxs-lookup"><span data-stu-id="a8b85-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="a8b85-124">Работа с классом ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="a8b85-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="a8b85-125">Строки в кодировке Base64 можно использовать для представления двоичных данных.</span><span class="sxs-lookup"><span data-stu-id="a8b85-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="a8b85-126">Например, изображение можно закодировать в виде строки.</span><span class="sxs-lookup"><span data-stu-id="a8b85-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="a8b85-127">Пример содержит изображение в виде строки в кодировке Base64 в файле [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="a8b85-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="a8b85-128">ASP.NET Core MVC может принимать строки в кодировке Base64 и использовать `ByteArrayModelBinder` для их преобразования в массив байтов.</span><span class="sxs-lookup"><span data-stu-id="a8b85-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="a8b85-129">Класс <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> сопоставляет аргументы `byte[]` с `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="a8b85-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

<span data-ttu-id="a8b85-130">При создании собственного настраиваемого связывателя модели можно реализовать собственный тип `IModelBinderProvider` или воспользоваться <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span><span class="sxs-lookup"><span data-stu-id="a8b85-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="a8b85-131">В следующем примере показано, как использовать `ByteArrayModelBinder` для преобразования строки в кодировке Base64 в `byte[]` и сохранить результат в файл:</span><span class="sxs-lookup"><span data-stu-id="a8b85-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="a8b85-132">Строку в кодировке Base64 можно отправить (POST) в этот метод API с помощью такого средства, как [Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="a8b85-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="a8b85-133">![Postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="a8b85-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="a8b85-134">Привязка модели успешно выполняется до тех пор, пока связыватель может привязывать данные запроса к свойствам или аргументам с соответствующими именами.</span><span class="sxs-lookup"><span data-stu-id="a8b85-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="a8b85-135">В приведенном ниже примере показано, как использовать `ByteArrayModelBinder` с моделью представления.</span><span class="sxs-lookup"><span data-stu-id="a8b85-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="a8b85-136">Образец настраиваемого связывателя модели</span><span class="sxs-lookup"><span data-stu-id="a8b85-136">Custom model binder sample</span></span>

<span data-ttu-id="a8b85-137">В этом разделе будет реализован настраиваемый связыватель модели, который выполняет следующий действия:</span><span class="sxs-lookup"><span data-stu-id="a8b85-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="a8b85-138">преобразует данные входящего запроса в строго типизированные аргументы ключа;</span><span class="sxs-lookup"><span data-stu-id="a8b85-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="a8b85-139">использует Entity Framework Core для получения связанной сущности;</span><span class="sxs-lookup"><span data-stu-id="a8b85-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="a8b85-140">передает связанную сущность в качестве аргумента в метод действия.</span><span class="sxs-lookup"><span data-stu-id="a8b85-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="a8b85-141">В следующем примере используется атрибут `ModelBinder` в модели `Author`:</span><span class="sxs-lookup"><span data-stu-id="a8b85-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="a8b85-142">В приведенном выше коде атрибут `ModelBinder` указывает тип `IModelBinder`, который следует использовать для привязки параметров действия `Author`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="a8b85-143">Следующий класс `AuthorEntityBinder` привязывает параметр `Author` путем получения сущности из источника данных с помощью Entity Framework Core и `authorId`:</span><span class="sxs-lookup"><span data-stu-id="a8b85-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="a8b85-144">Предыдущий класс `AuthorEntityBinder` предназначен для демонстрации пользовательского связывателя модели.</span><span class="sxs-lookup"><span data-stu-id="a8b85-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="a8b85-145">Класс не предназначен для демонстрации рекомендаций для использования сценария просмотра.</span><span class="sxs-lookup"><span data-stu-id="a8b85-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="a8b85-146">Чтобы выполнить просмотр, привяжите `authorId` и отправьте запрос к базе данных в методе действия.</span><span class="sxs-lookup"><span data-stu-id="a8b85-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="a8b85-147">Этот подход отделяет ошибки привязки модели от вариантов `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="a8b85-148">В следующем примере кода демонстрируется использование `AuthorEntityBinder` в методе действия.</span><span class="sxs-lookup"><span data-stu-id="a8b85-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="a8b85-149">С помощью атрибута `ModelBinder` можно применять `AuthorEntityBinder` к параметрам, которые не используют соглашения по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="a8b85-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="a8b85-150">Поскольку в этом примере имя аргумента не является `authorId` по умолчанию, оно указывается в параметре с помощью атрибута `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="a8b85-151">Возможности контроллера и метода действия упрощены по сравнению с функцией поиска сущности в методе действия.</span><span class="sxs-lookup"><span data-stu-id="a8b85-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="a8b85-152">Логика для выборки автора с использованием Entity Framework Core перемещается в связыватель модели.</span><span class="sxs-lookup"><span data-stu-id="a8b85-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="a8b85-153">Такой подход позволит существенно упростить работу, если у вас есть несколько методов для привязки к модели `Author`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="a8b85-154">Чтобы указать определенный связыватель модели или имя только для данного типа или действия, можно применить атрибут `ModelBinder` к свойствам отдельной модели (таким как viewmodel) или к параметрам метода действия.</span><span class="sxs-lookup"><span data-stu-id="a8b85-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="a8b85-155">Реализация класса ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="a8b85-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="a8b85-156">Вместо применения атрибута можно реализовать класс `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="a8b85-157">Таким образом реализуются встроенные связыватели платформы.</span><span class="sxs-lookup"><span data-stu-id="a8b85-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="a8b85-158">При указании типа, с которым работает связыватель, определяется тип создаваемого им аргумента, а **не** принимаемые им входные данные.</span><span class="sxs-lookup"><span data-stu-id="a8b85-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="a8b85-159">Следующий поставщик связывателей работает с `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="a8b85-160">Если он добавляется в коллекцию поставщиков MVC, не нужно использовать атрибут `ModelBinder` в типизированных параметрах `Author` или `Author`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="a8b85-161">Примечание. Приведенный выше код возвращает `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="a8b85-162">`BinderTypeModelBinder` выступает в качестве фабрики для связывателей моделей и обеспечивает внедрение зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="a8b85-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="a8b85-163">Для доступа к EF Core классу `AuthorEntityBinder` требуется внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a8b85-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="a8b85-164">Если связывателю модели необходимы службы из DI, используйте класс `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="a8b85-165">Чтобы начать работу с настраиваемым поставщиком связывателей моделей, добавьте его в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a8b85-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="a8b85-166">При оценке связывателей моделей коллекция поставщиков проверяется в определенном порядке.</span><span class="sxs-lookup"><span data-stu-id="a8b85-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="a8b85-167">Используется первый поставщик, который возвращает связыватель.</span><span class="sxs-lookup"><span data-stu-id="a8b85-167">The first provider that returns a binder is used.</span></span> <span data-ttu-id="a8b85-168">Добавление поставщика в конец коллекции может привести к вызову встроенного связывателя модели раньше, чем будет вызван ваш собственный настраиваемый связыватель.</span><span class="sxs-lookup"><span data-stu-id="a8b85-168">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="a8b85-169">В этом примере настраиваемый поставщик добавляется в начало коллекции, чтобы использоваться для аргументов действия `Author`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-169">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="a8b85-170">Полиморфная привязка моделей</span><span class="sxs-lookup"><span data-stu-id="a8b85-170">Polymorphic model binding</span></span>

<span data-ttu-id="a8b85-171">Привязка к разным моделям производных типов называется полиморфной привязкой модели.</span><span class="sxs-lookup"><span data-stu-id="a8b85-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="a8b85-172">Полиморфная пользовательская привязка модели требуется, когда значение запроса должно быть привязано к конкретному типу производной модели.</span><span class="sxs-lookup"><span data-stu-id="a8b85-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="a8b85-173">Характеристики полиморфной привязки моделей:</span><span class="sxs-lookup"><span data-stu-id="a8b85-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="a8b85-174">не является типичной для REST API, предназначенного для взаимодействия со всеми языками;</span><span class="sxs-lookup"><span data-stu-id="a8b85-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="a8b85-175">усложняет понимание того, как работают модели привязок.</span><span class="sxs-lookup"><span data-stu-id="a8b85-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="a8b85-176">Но если приложению требуется полиморфная привязка модели, реализация может выглядеть так, как в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="a8b85-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="a8b85-177">Рекомендации и советы</span><span class="sxs-lookup"><span data-stu-id="a8b85-177">Recommendations and best practices</span></span>

<span data-ttu-id="a8b85-178">Ниже приведены рекомендации относительно настраиваемых связывателей моделей.</span><span class="sxs-lookup"><span data-stu-id="a8b85-178">Custom model binders:</span></span>

- <span data-ttu-id="a8b85-179">Связыватели моделей не следует использовать для установки кодов состояния или возвращаемых результатов (например, "404 — не найдено").</span><span class="sxs-lookup"><span data-stu-id="a8b85-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="a8b85-180">При сбое привязки модели обрабатывать ошибку должен сам [фильтр действий](xref:mvc/controllers/filters) или логика в самом методе действия.</span><span class="sxs-lookup"><span data-stu-id="a8b85-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="a8b85-181">Связыватели наиболее полезны в сценариях исключения повторяющихся частей кода и решения взаимосвязанных проблем с методами действий.</span><span class="sxs-lookup"><span data-stu-id="a8b85-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="a8b85-182">Как правило, связыватели не следует использовать для преобразования строки в пользовательский тип. Лучшим вариантом обычно является <xref:System.ComponentModel.TypeConverter>.</span><span class="sxs-lookup"><span data-stu-id="a8b85-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a8b85-183">Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="a8b85-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a8b85-184">Привязка модели позволяет действиям контроллера работать непосредственно с типами моделей (передаваемыми в качестве аргументов метода), а не с HTTP-запросами.</span><span class="sxs-lookup"><span data-stu-id="a8b85-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="a8b85-185">Сопоставление между данными входящего запроса и моделями приложений обрабатывается связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="a8b85-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="a8b85-186">Разработчики могут расширить функциональность привязки встроенных модели путем реализации настраиваемых связывателей (хотя обычно создавать собственный поставщик не требуется).</span><span class="sxs-lookup"><span data-stu-id="a8b85-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="a8b85-187">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a8b85-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="a8b85-188">Ограничения для связывателя модели по умолчанию</span><span class="sxs-lookup"><span data-stu-id="a8b85-188">Default model binder limitations</span></span>

<span data-ttu-id="a8b85-189">Связыватели моделей по умолчанию поддерживают значительную часть распространенных типов данных .NET Core и должны соответствовать большинству требований разработчиков.</span><span class="sxs-lookup"><span data-stu-id="a8b85-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="a8b85-190">Ожидается, что они будут привязывать текстовые входные данные из запроса непосредственно к типам моделей.</span><span class="sxs-lookup"><span data-stu-id="a8b85-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="a8b85-191">Перед привязкой может потребоваться преобразовать входные данные.</span><span class="sxs-lookup"><span data-stu-id="a8b85-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="a8b85-192">Например, у вас есть ключ, используемый для поиска данных модели.</span><span class="sxs-lookup"><span data-stu-id="a8b85-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="a8b85-193">Для выборки данных на основе ключа можно воспользоваться настраиваемым связывателем модели.</span><span class="sxs-lookup"><span data-stu-id="a8b85-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="a8b85-194">Обзор привязки модели</span><span class="sxs-lookup"><span data-stu-id="a8b85-194">Model binding review</span></span>

<span data-ttu-id="a8b85-195">Для типов, с которыми работает привязка данных, используются специальные определения.</span><span class="sxs-lookup"><span data-stu-id="a8b85-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="a8b85-196">*Простой тип* преобразуется из одной строки во входных данных.</span><span class="sxs-lookup"><span data-stu-id="a8b85-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="a8b85-197">*Сложный тип* преобразуется из нескольких входных значений.</span><span class="sxs-lookup"><span data-stu-id="a8b85-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="a8b85-198">Платформа определяет их различие в зависимости от наличия `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="a8b85-199">Если у вас есть простое сопоставление `string` -> `SomeType`, не требующее внешних ресурсов, рекомендуется создать преобразователь типов.</span><span class="sxs-lookup"><span data-stu-id="a8b85-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="a8b85-200">Прежде чем создавать собственный настраиваемый связыватель модели, следует понять реализацию существующих связывателей моделей.</span><span class="sxs-lookup"><span data-stu-id="a8b85-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="a8b85-201">Рассмотрим класс <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, который используется для преобразования строк в кодировке Base64 в массивы байтов.</span><span class="sxs-lookup"><span data-stu-id="a8b85-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="a8b85-202">Массивы байтов часто хранятся в виде файлов или полей больших двоичных объектов базы данных.</span><span class="sxs-lookup"><span data-stu-id="a8b85-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="a8b85-203">Работа с классом ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="a8b85-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="a8b85-204">Строки в кодировке Base64 можно использовать для представления двоичных данных.</span><span class="sxs-lookup"><span data-stu-id="a8b85-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="a8b85-205">Например, изображение можно закодировать в виде строки.</span><span class="sxs-lookup"><span data-stu-id="a8b85-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="a8b85-206">Пример содержит изображение в виде строки в кодировке Base64 в файле [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="a8b85-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="a8b85-207">ASP.NET Core MVC может принимать строки в кодировке Base64 и использовать `ByteArrayModelBinder` для их преобразования в массив байтов.</span><span class="sxs-lookup"><span data-stu-id="a8b85-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="a8b85-208">Класс <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> сопоставляет аргументы `byte[]` с `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="a8b85-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="a8b85-209">При создании собственного настраиваемого связывателя модели можно реализовать собственный тип `IModelBinderProvider` или воспользоваться <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span><span class="sxs-lookup"><span data-stu-id="a8b85-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="a8b85-210">В следующем примере показано, как использовать `ByteArrayModelBinder` для преобразования строки в кодировке Base64 в `byte[]` и сохранить результат в файл:</span><span class="sxs-lookup"><span data-stu-id="a8b85-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="a8b85-211">Строку в кодировке Base64 можно отправить (POST) в этот метод API с помощью такого средства, как [Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="a8b85-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="a8b85-212">![Postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="a8b85-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="a8b85-213">Привязка модели успешно выполняется до тех пор, пока связыватель может привязывать данные запроса к свойствам или аргументам с соответствующими именами.</span><span class="sxs-lookup"><span data-stu-id="a8b85-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="a8b85-214">В приведенном ниже примере показано, как использовать `ByteArrayModelBinder` с моделью представления.</span><span class="sxs-lookup"><span data-stu-id="a8b85-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="a8b85-215">Образец настраиваемого связывателя модели</span><span class="sxs-lookup"><span data-stu-id="a8b85-215">Custom model binder sample</span></span>

<span data-ttu-id="a8b85-216">В этом разделе будет реализован настраиваемый связыватель модели, который выполняет следующий действия:</span><span class="sxs-lookup"><span data-stu-id="a8b85-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="a8b85-217">преобразует данные входящего запроса в строго типизированные аргументы ключа;</span><span class="sxs-lookup"><span data-stu-id="a8b85-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="a8b85-218">использует Entity Framework Core для получения связанной сущности;</span><span class="sxs-lookup"><span data-stu-id="a8b85-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="a8b85-219">передает связанную сущность в качестве аргумента в метод действия.</span><span class="sxs-lookup"><span data-stu-id="a8b85-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="a8b85-220">В следующем примере используется атрибут `ModelBinder` в модели `Author`:</span><span class="sxs-lookup"><span data-stu-id="a8b85-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="a8b85-221">В приведенном выше коде атрибут `ModelBinder` указывает тип `IModelBinder`, который следует использовать для привязки параметров действия `Author`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="a8b85-222">Следующий класс `AuthorEntityBinder` привязывает параметр `Author` путем получения сущности из источника данных с помощью Entity Framework Core и `authorId`:</span><span class="sxs-lookup"><span data-stu-id="a8b85-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="a8b85-223">Предыдущий класс `AuthorEntityBinder` предназначен для демонстрации пользовательского связывателя модели.</span><span class="sxs-lookup"><span data-stu-id="a8b85-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="a8b85-224">Класс не предназначен для демонстрации рекомендаций для использования сценария просмотра.</span><span class="sxs-lookup"><span data-stu-id="a8b85-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="a8b85-225">Чтобы выполнить просмотр, привяжите `authorId` и отправьте запрос к базе данных в методе действия.</span><span class="sxs-lookup"><span data-stu-id="a8b85-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="a8b85-226">Этот подход отделяет ошибки привязки модели от вариантов `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="a8b85-227">В следующем примере кода демонстрируется использование `AuthorEntityBinder` в методе действия.</span><span class="sxs-lookup"><span data-stu-id="a8b85-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="a8b85-228">С помощью атрибута `ModelBinder` можно применять `AuthorEntityBinder` к параметрам, которые не используют соглашения по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="a8b85-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="a8b85-229">Поскольку в этом примере имя аргумента не является `authorId` по умолчанию, оно указывается в параметре с помощью атрибута `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="a8b85-230">Возможности контроллера и метода действия упрощены по сравнению с функцией поиска сущности в методе действия.</span><span class="sxs-lookup"><span data-stu-id="a8b85-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="a8b85-231">Логика для выборки автора с использованием Entity Framework Core перемещается в связыватель модели.</span><span class="sxs-lookup"><span data-stu-id="a8b85-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="a8b85-232">Такой подход позволит существенно упростить работу, если у вас есть несколько методов для привязки к модели `Author`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="a8b85-233">Чтобы указать определенный связыватель модели или имя только для данного типа или действия, можно применить атрибут `ModelBinder` к свойствам отдельной модели (таким как viewmodel) или к параметрам метода действия.</span><span class="sxs-lookup"><span data-stu-id="a8b85-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="a8b85-234">Реализация класса ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="a8b85-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="a8b85-235">Вместо применения атрибута можно реализовать класс `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="a8b85-236">Таким образом реализуются встроенные связыватели платформы.</span><span class="sxs-lookup"><span data-stu-id="a8b85-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="a8b85-237">При указании типа, с которым работает связыватель, определяется тип создаваемого им аргумента, а **не** принимаемые им входные данные.</span><span class="sxs-lookup"><span data-stu-id="a8b85-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="a8b85-238">Следующий поставщик связывателей работает с `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="a8b85-239">Если он добавляется в коллекцию поставщиков MVC, не нужно использовать атрибут `ModelBinder` в типизированных параметрах `Author` или `Author`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="a8b85-240">Примечание. Приведенный выше код возвращает `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="a8b85-241">`BinderTypeModelBinder` выступает в качестве фабрики для связывателей моделей и обеспечивает внедрение зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="a8b85-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="a8b85-242">Для доступа к EF Core классу `AuthorEntityBinder` требуется внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a8b85-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="a8b85-243">Если связывателю модели необходимы службы из DI, используйте класс `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="a8b85-244">Чтобы начать работу с настраиваемым поставщиком связывателей моделей, добавьте его в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a8b85-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="a8b85-245">При оценке связывателей моделей коллекция поставщиков проверяется в определенном порядке.</span><span class="sxs-lookup"><span data-stu-id="a8b85-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="a8b85-246">Используется первый поставщик, который возвращает связыватель.</span><span class="sxs-lookup"><span data-stu-id="a8b85-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="a8b85-247">Добавление поставщика в конец коллекции может привести к вызову встроенного связывателя модели раньше, чем будет вызван ваш собственный настраиваемый связыватель.</span><span class="sxs-lookup"><span data-stu-id="a8b85-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="a8b85-248">В этом примере настраиваемый поставщик добавляется в начало коллекции, чтобы использоваться для аргументов действия `Author`.</span><span class="sxs-lookup"><span data-stu-id="a8b85-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="a8b85-249">Полиморфная привязка моделей</span><span class="sxs-lookup"><span data-stu-id="a8b85-249">Polymorphic model binding</span></span>

<span data-ttu-id="a8b85-250">Привязка к разным моделям производных типов называется полиморфной привязкой модели.</span><span class="sxs-lookup"><span data-stu-id="a8b85-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="a8b85-251">Полиморфная пользовательская привязка модели требуется, когда значение запроса должно быть привязано к конкретному типу производной модели.</span><span class="sxs-lookup"><span data-stu-id="a8b85-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="a8b85-252">Характеристики полиморфной привязки моделей:</span><span class="sxs-lookup"><span data-stu-id="a8b85-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="a8b85-253">не является типичной для REST API, предназначенного для взаимодействия со всеми языками;</span><span class="sxs-lookup"><span data-stu-id="a8b85-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="a8b85-254">усложняет понимание того, как работают модели привязок.</span><span class="sxs-lookup"><span data-stu-id="a8b85-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="a8b85-255">Но если приложению требуется полиморфная привязка модели, реализация может выглядеть так, как в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="a8b85-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="a8b85-256">Рекомендации и советы</span><span class="sxs-lookup"><span data-stu-id="a8b85-256">Recommendations and best practices</span></span>

<span data-ttu-id="a8b85-257">Ниже приведены рекомендации относительно настраиваемых связывателей моделей.</span><span class="sxs-lookup"><span data-stu-id="a8b85-257">Custom model binders:</span></span>

- <span data-ttu-id="a8b85-258">Связыватели моделей не следует использовать для установки кодов состояния или возвращаемых результатов (например, "404 — не найдено").</span><span class="sxs-lookup"><span data-stu-id="a8b85-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="a8b85-259">При сбое привязки модели обрабатывать ошибку должен сам [фильтр действий](xref:mvc/controllers/filters) или логика в самом методе действия.</span><span class="sxs-lookup"><span data-stu-id="a8b85-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="a8b85-260">Связыватели наиболее полезны в сценариях исключения повторяющихся частей кода и решения взаимосвязанных проблем с методами действий.</span><span class="sxs-lookup"><span data-stu-id="a8b85-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="a8b85-261">Как правило, связыватели не следует использовать для преобразования строки в пользовательский тип. Лучшим вариантом обычно является <xref:System.ComponentModel.TypeConverter>.</span><span class="sxs-lookup"><span data-stu-id="a8b85-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
