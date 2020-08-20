---
title: Пользовательская привязка модели в ASP.NET Core
author: ardalis
description: Узнайте, как привязка модели позволяет действиям контроллера работать непосредственно с типами модели в ASP.NET Core.
ms.author: riande
ms.date: 01/06/2020
no-loc:
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 4bef68fffbdfaff023f71964a27ead56863e4192
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630436"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="1c347-103">Пользовательская привязка модели в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c347-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1c347-104">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="1c347-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="1c347-105">Привязка модели позволяет действиям контроллера работать непосредственно с типами моделей (передаваемыми в качестве аргументов метода), а не с HTTP-запросами.</span><span class="sxs-lookup"><span data-stu-id="1c347-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="1c347-106">Сопоставление между данными входящего запроса и моделями приложений обрабатывается связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="1c347-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="1c347-107">Разработчики могут расширить функциональность привязки встроенных модели путем реализации настраиваемых связывателей (хотя обычно создавать собственный поставщик не требуется).</span><span class="sxs-lookup"><span data-stu-id="1c347-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="1c347-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1c347-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="1c347-109">Ограничения для связывателя модели по умолчанию</span><span class="sxs-lookup"><span data-stu-id="1c347-109">Default model binder limitations</span></span>

<span data-ttu-id="1c347-110">Связыватели моделей по умолчанию поддерживают значительную часть распространенных типов данных .NET Core и должны соответствовать большинству требований разработчиков.</span><span class="sxs-lookup"><span data-stu-id="1c347-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="1c347-111">Ожидается, что они будут привязывать текстовые входные данные из запроса непосредственно к типам моделей.</span><span class="sxs-lookup"><span data-stu-id="1c347-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="1c347-112">Перед привязкой может потребоваться преобразовать входные данные.</span><span class="sxs-lookup"><span data-stu-id="1c347-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="1c347-113">Например, у вас есть ключ, используемый для поиска данных модели.</span><span class="sxs-lookup"><span data-stu-id="1c347-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="1c347-114">Для выборки данных на основе ключа можно воспользоваться настраиваемым связывателем модели.</span><span class="sxs-lookup"><span data-stu-id="1c347-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="1c347-115">Обзор привязки модели</span><span class="sxs-lookup"><span data-stu-id="1c347-115">Model binding review</span></span>

<span data-ttu-id="1c347-116">Для типов, с которыми работает привязка данных, используются специальные определения.</span><span class="sxs-lookup"><span data-stu-id="1c347-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="1c347-117">*Простой тип* преобразуется из одной строки во входных данных.</span><span class="sxs-lookup"><span data-stu-id="1c347-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="1c347-118">*Сложный тип* преобразуется из нескольких входных значений.</span><span class="sxs-lookup"><span data-stu-id="1c347-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="1c347-119">Платформа определяет их различие в зависимости от наличия `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="1c347-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="1c347-120">Если у вас есть простое сопоставление `string` -> `SomeType`, не требующее внешних ресурсов, рекомендуется создать преобразователь типов.</span><span class="sxs-lookup"><span data-stu-id="1c347-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="1c347-121">Прежде чем создавать собственный настраиваемый связыватель модели, следует понять реализацию существующих связывателей моделей.</span><span class="sxs-lookup"><span data-stu-id="1c347-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="1c347-122">Рассмотрим класс <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, который используется для преобразования строк в кодировке Base64 в массивы байтов.</span><span class="sxs-lookup"><span data-stu-id="1c347-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="1c347-123">Массивы байтов часто хранятся в виде файлов или полей больших двоичных объектов базы данных.</span><span class="sxs-lookup"><span data-stu-id="1c347-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="1c347-124">Работа с классом ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="1c347-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="1c347-125">Строки в кодировке Base64 можно использовать для представления двоичных данных.</span><span class="sxs-lookup"><span data-stu-id="1c347-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="1c347-126">Например, изображение можно закодировать в виде строки.</span><span class="sxs-lookup"><span data-stu-id="1c347-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="1c347-127">Пример содержит изображение в виде строки в кодировке Base64 в файле [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="1c347-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="1c347-128">ASP.NET Core MVC может принимать строки в кодировке Base64 и использовать `ByteArrayModelBinder` для их преобразования в массив байтов.</span><span class="sxs-lookup"><span data-stu-id="1c347-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="1c347-129">Класс <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> сопоставляет аргументы `byte[]` с `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="1c347-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

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

<span data-ttu-id="1c347-130">При создании собственного настраиваемого связывателя модели можно реализовать собственный тип `IModelBinderProvider` или воспользоваться <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span><span class="sxs-lookup"><span data-stu-id="1c347-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="1c347-131">В следующем примере показано, как использовать `ByteArrayModelBinder` для преобразования строки в кодировке Base64 в `byte[]` и сохранить результат в файл:</span><span class="sxs-lookup"><span data-stu-id="1c347-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="1c347-132">Строку в кодировке Base64 можно отправить (POST) в этот метод API с помощью такого средства, как [Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="1c347-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="1c347-133">![Postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="1c347-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="1c347-134">Привязка модели успешно выполняется до тех пор, пока связыватель может привязывать данные запроса к свойствам или аргументам с соответствующими именами.</span><span class="sxs-lookup"><span data-stu-id="1c347-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="1c347-135">В приведенном ниже примере показано, как использовать `ByteArrayModelBinder` с моделью представления.</span><span class="sxs-lookup"><span data-stu-id="1c347-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="1c347-136">Образец настраиваемого связывателя модели</span><span class="sxs-lookup"><span data-stu-id="1c347-136">Custom model binder sample</span></span>

<span data-ttu-id="1c347-137">В этом разделе будет реализован настраиваемый связыватель модели, который выполняет следующий действия:</span><span class="sxs-lookup"><span data-stu-id="1c347-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="1c347-138">преобразует данные входящего запроса в строго типизированные аргументы ключа;</span><span class="sxs-lookup"><span data-stu-id="1c347-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="1c347-139">использует Entity Framework Core для получения связанной сущности;</span><span class="sxs-lookup"><span data-stu-id="1c347-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="1c347-140">передает связанную сущность в качестве аргумента в метод действия.</span><span class="sxs-lookup"><span data-stu-id="1c347-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="1c347-141">В следующем примере используется атрибут `ModelBinder` в модели `Author`:</span><span class="sxs-lookup"><span data-stu-id="1c347-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="1c347-142">В приведенном выше коде атрибут `ModelBinder` указывает тип `IModelBinder`, который следует использовать для привязки параметров действия `Author`.</span><span class="sxs-lookup"><span data-stu-id="1c347-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="1c347-143">Следующий класс `AuthorEntityBinder` привязывает параметр `Author` путем получения сущности из источника данных с помощью Entity Framework Core и `authorId`:</span><span class="sxs-lookup"><span data-stu-id="1c347-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="1c347-144">Предыдущий класс `AuthorEntityBinder` предназначен для демонстрации пользовательского связывателя модели.</span><span class="sxs-lookup"><span data-stu-id="1c347-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="1c347-145">Класс не предназначен для демонстрации рекомендаций для использования сценария просмотра.</span><span class="sxs-lookup"><span data-stu-id="1c347-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="1c347-146">Чтобы выполнить просмотр, привяжите `authorId` и отправьте запрос к базе данных в методе действия.</span><span class="sxs-lookup"><span data-stu-id="1c347-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="1c347-147">Этот подход отделяет ошибки привязки модели от вариантов `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="1c347-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="1c347-148">В следующем примере кода демонстрируется использование `AuthorEntityBinder` в методе действия.</span><span class="sxs-lookup"><span data-stu-id="1c347-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="1c347-149">С помощью атрибута `ModelBinder` можно применять `AuthorEntityBinder` к параметрам, которые не используют соглашения по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="1c347-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="1c347-150">Поскольку в этом примере имя аргумента не является `authorId` по умолчанию, оно указывается в параметре с помощью атрибута `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="1c347-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="1c347-151">Возможности контроллера и метода действия упрощены по сравнению с функцией поиска сущности в методе действия.</span><span class="sxs-lookup"><span data-stu-id="1c347-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="1c347-152">Логика для выборки автора с использованием Entity Framework Core перемещается в связыватель модели.</span><span class="sxs-lookup"><span data-stu-id="1c347-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="1c347-153">Такой подход позволит существенно упростить работу, если у вас есть несколько методов для привязки к модели `Author`.</span><span class="sxs-lookup"><span data-stu-id="1c347-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="1c347-154">Чтобы указать определенный связыватель модели или имя только для данного типа или действия, можно применить атрибут `ModelBinder` к свойствам отдельной модели (таким как viewmodel) или к параметрам метода действия.</span><span class="sxs-lookup"><span data-stu-id="1c347-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="1c347-155">Реализация класса ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="1c347-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="1c347-156">Вместо применения атрибута можно реализовать класс `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="1c347-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="1c347-157">Таким образом реализуются встроенные связыватели платформы.</span><span class="sxs-lookup"><span data-stu-id="1c347-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="1c347-158">При указании типа, с которым работает связыватель, определяется тип создаваемого им аргумента, а **не** принимаемые им входные данные.</span><span class="sxs-lookup"><span data-stu-id="1c347-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="1c347-159">Следующий поставщик связывателей работает с `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="1c347-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="1c347-160">Если он добавляется в коллекцию поставщиков MVC, не нужно использовать атрибут `ModelBinder` в типизированных параметрах `Author` или `Author`.</span><span class="sxs-lookup"><span data-stu-id="1c347-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="1c347-161">Примечание. Приведенный выше код возвращает `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="1c347-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="1c347-162">`BinderTypeModelBinder` выступает в качестве фабрики для связывателей моделей и обеспечивает внедрение зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="1c347-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="1c347-163">Для доступа к EF Core классу `AuthorEntityBinder` требуется внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="1c347-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="1c347-164">Если связывателю модели необходимы службы из DI, используйте класс `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="1c347-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="1c347-165">Чтобы начать работу с настраиваемым поставщиком связывателей моделей, добавьте его в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1c347-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="1c347-166">При оценке связывателей моделей коллекция поставщиков проверяется в определенном порядке.</span><span class="sxs-lookup"><span data-stu-id="1c347-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="1c347-167">Используется первый поставщик, который возвращает связыватель.</span><span class="sxs-lookup"><span data-stu-id="1c347-167">The first provider that returns a binder is used.</span></span> <span data-ttu-id="1c347-168">Добавление поставщика в конец коллекции может привести к вызову встроенного связывателя модели раньше, чем будет вызван ваш собственный настраиваемый связыватель.</span><span class="sxs-lookup"><span data-stu-id="1c347-168">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="1c347-169">В этом примере настраиваемый поставщик добавляется в начало коллекции, чтобы использоваться для аргументов действия `Author`.</span><span class="sxs-lookup"><span data-stu-id="1c347-169">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="1c347-170">Полиморфная привязка моделей</span><span class="sxs-lookup"><span data-stu-id="1c347-170">Polymorphic model binding</span></span>

<span data-ttu-id="1c347-171">Привязка к разным моделям производных типов называется полиморфной привязкой модели.</span><span class="sxs-lookup"><span data-stu-id="1c347-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="1c347-172">Полиморфная пользовательская привязка модели требуется, когда значение запроса должно быть привязано к конкретному типу производной модели.</span><span class="sxs-lookup"><span data-stu-id="1c347-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="1c347-173">Характеристики полиморфной привязки моделей:</span><span class="sxs-lookup"><span data-stu-id="1c347-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="1c347-174">не является типичной для REST API, предназначенного для взаимодействия со всеми языками;</span><span class="sxs-lookup"><span data-stu-id="1c347-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="1c347-175">усложняет понимание того, как работают модели привязок.</span><span class="sxs-lookup"><span data-stu-id="1c347-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="1c347-176">Но если приложению требуется полиморфная привязка модели, реализация может выглядеть так, как в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="1c347-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="1c347-177">Рекомендации и советы</span><span class="sxs-lookup"><span data-stu-id="1c347-177">Recommendations and best practices</span></span>

<span data-ttu-id="1c347-178">Ниже приведены рекомендации относительно настраиваемых связывателей моделей.</span><span class="sxs-lookup"><span data-stu-id="1c347-178">Custom model binders:</span></span>

- <span data-ttu-id="1c347-179">Связыватели моделей не следует использовать для установки кодов состояния или возвращаемых результатов (например, "404 — не найдено").</span><span class="sxs-lookup"><span data-stu-id="1c347-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="1c347-180">При сбое привязки модели обрабатывать ошибку должен сам [фильтр действий](xref:mvc/controllers/filters) или логика в самом методе действия.</span><span class="sxs-lookup"><span data-stu-id="1c347-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="1c347-181">Связыватели наиболее полезны в сценариях исключения повторяющихся частей кода и решения взаимосвязанных проблем с методами действий.</span><span class="sxs-lookup"><span data-stu-id="1c347-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="1c347-182">Как правило, связыватели не следует использовать для преобразования строки в пользовательский тип. Лучшим вариантом обычно является <xref:System.ComponentModel.TypeConverter>.</span><span class="sxs-lookup"><span data-stu-id="1c347-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1c347-183">Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="1c347-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1c347-184">Привязка модели позволяет действиям контроллера работать непосредственно с типами моделей (передаваемыми в качестве аргументов метода), а не с HTTP-запросами.</span><span class="sxs-lookup"><span data-stu-id="1c347-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="1c347-185">Сопоставление между данными входящего запроса и моделями приложений обрабатывается связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="1c347-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="1c347-186">Разработчики могут расширить функциональность привязки встроенных модели путем реализации настраиваемых связывателей (хотя обычно создавать собственный поставщик не требуется).</span><span class="sxs-lookup"><span data-stu-id="1c347-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="1c347-187">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1c347-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="1c347-188">Ограничения для связывателя модели по умолчанию</span><span class="sxs-lookup"><span data-stu-id="1c347-188">Default model binder limitations</span></span>

<span data-ttu-id="1c347-189">Связыватели моделей по умолчанию поддерживают значительную часть распространенных типов данных .NET Core и должны соответствовать большинству требований разработчиков.</span><span class="sxs-lookup"><span data-stu-id="1c347-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="1c347-190">Ожидается, что они будут привязывать текстовые входные данные из запроса непосредственно к типам моделей.</span><span class="sxs-lookup"><span data-stu-id="1c347-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="1c347-191">Перед привязкой может потребоваться преобразовать входные данные.</span><span class="sxs-lookup"><span data-stu-id="1c347-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="1c347-192">Например, у вас есть ключ, используемый для поиска данных модели.</span><span class="sxs-lookup"><span data-stu-id="1c347-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="1c347-193">Для выборки данных на основе ключа можно воспользоваться настраиваемым связывателем модели.</span><span class="sxs-lookup"><span data-stu-id="1c347-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="1c347-194">Обзор привязки модели</span><span class="sxs-lookup"><span data-stu-id="1c347-194">Model binding review</span></span>

<span data-ttu-id="1c347-195">Для типов, с которыми работает привязка данных, используются специальные определения.</span><span class="sxs-lookup"><span data-stu-id="1c347-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="1c347-196">*Простой тип* преобразуется из одной строки во входных данных.</span><span class="sxs-lookup"><span data-stu-id="1c347-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="1c347-197">*Сложный тип* преобразуется из нескольких входных значений.</span><span class="sxs-lookup"><span data-stu-id="1c347-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="1c347-198">Платформа определяет их различие в зависимости от наличия `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="1c347-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="1c347-199">Если у вас есть простое сопоставление `string` -> `SomeType`, не требующее внешних ресурсов, рекомендуется создать преобразователь типов.</span><span class="sxs-lookup"><span data-stu-id="1c347-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="1c347-200">Прежде чем создавать собственный настраиваемый связыватель модели, следует понять реализацию существующих связывателей моделей.</span><span class="sxs-lookup"><span data-stu-id="1c347-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="1c347-201">Рассмотрим класс <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, который используется для преобразования строк в кодировке Base64 в массивы байтов.</span><span class="sxs-lookup"><span data-stu-id="1c347-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="1c347-202">Массивы байтов часто хранятся в виде файлов или полей больших двоичных объектов базы данных.</span><span class="sxs-lookup"><span data-stu-id="1c347-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="1c347-203">Работа с классом ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="1c347-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="1c347-204">Строки в кодировке Base64 можно использовать для представления двоичных данных.</span><span class="sxs-lookup"><span data-stu-id="1c347-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="1c347-205">Например, изображение можно закодировать в виде строки.</span><span class="sxs-lookup"><span data-stu-id="1c347-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="1c347-206">Пример содержит изображение в виде строки в кодировке Base64 в файле [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="1c347-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="1c347-207">ASP.NET Core MVC может принимать строки в кодировке Base64 и использовать `ByteArrayModelBinder` для их преобразования в массив байтов.</span><span class="sxs-lookup"><span data-stu-id="1c347-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="1c347-208">Класс <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> сопоставляет аргументы `byte[]` с `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="1c347-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

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

<span data-ttu-id="1c347-209">При создании собственного настраиваемого связывателя модели можно реализовать собственный тип `IModelBinderProvider` или воспользоваться <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span><span class="sxs-lookup"><span data-stu-id="1c347-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="1c347-210">В следующем примере показано, как использовать `ByteArrayModelBinder` для преобразования строки в кодировке Base64 в `byte[]` и сохранить результат в файл:</span><span class="sxs-lookup"><span data-stu-id="1c347-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="1c347-211">Строку в кодировке Base64 можно отправить (POST) в этот метод API с помощью такого средства, как [Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="1c347-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="1c347-212">![Postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="1c347-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="1c347-213">Привязка модели успешно выполняется до тех пор, пока связыватель может привязывать данные запроса к свойствам или аргументам с соответствующими именами.</span><span class="sxs-lookup"><span data-stu-id="1c347-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="1c347-214">В приведенном ниже примере показано, как использовать `ByteArrayModelBinder` с моделью представления.</span><span class="sxs-lookup"><span data-stu-id="1c347-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="1c347-215">Образец настраиваемого связывателя модели</span><span class="sxs-lookup"><span data-stu-id="1c347-215">Custom model binder sample</span></span>

<span data-ttu-id="1c347-216">В этом разделе будет реализован настраиваемый связыватель модели, который выполняет следующий действия:</span><span class="sxs-lookup"><span data-stu-id="1c347-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="1c347-217">преобразует данные входящего запроса в строго типизированные аргументы ключа;</span><span class="sxs-lookup"><span data-stu-id="1c347-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="1c347-218">использует Entity Framework Core для получения связанной сущности;</span><span class="sxs-lookup"><span data-stu-id="1c347-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="1c347-219">передает связанную сущность в качестве аргумента в метод действия.</span><span class="sxs-lookup"><span data-stu-id="1c347-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="1c347-220">В следующем примере используется атрибут `ModelBinder` в модели `Author`:</span><span class="sxs-lookup"><span data-stu-id="1c347-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="1c347-221">В приведенном выше коде атрибут `ModelBinder` указывает тип `IModelBinder`, который следует использовать для привязки параметров действия `Author`.</span><span class="sxs-lookup"><span data-stu-id="1c347-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="1c347-222">Следующий класс `AuthorEntityBinder` привязывает параметр `Author` путем получения сущности из источника данных с помощью Entity Framework Core и `authorId`:</span><span class="sxs-lookup"><span data-stu-id="1c347-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="1c347-223">Предыдущий класс `AuthorEntityBinder` предназначен для демонстрации пользовательского связывателя модели.</span><span class="sxs-lookup"><span data-stu-id="1c347-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="1c347-224">Класс не предназначен для демонстрации рекомендаций для использования сценария просмотра.</span><span class="sxs-lookup"><span data-stu-id="1c347-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="1c347-225">Чтобы выполнить просмотр, привяжите `authorId` и отправьте запрос к базе данных в методе действия.</span><span class="sxs-lookup"><span data-stu-id="1c347-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="1c347-226">Этот подход отделяет ошибки привязки модели от вариантов `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="1c347-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="1c347-227">В следующем примере кода демонстрируется использование `AuthorEntityBinder` в методе действия.</span><span class="sxs-lookup"><span data-stu-id="1c347-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="1c347-228">С помощью атрибута `ModelBinder` можно применять `AuthorEntityBinder` к параметрам, которые не используют соглашения по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="1c347-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="1c347-229">Поскольку в этом примере имя аргумента не является `authorId` по умолчанию, оно указывается в параметре с помощью атрибута `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="1c347-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="1c347-230">Возможности контроллера и метода действия упрощены по сравнению с функцией поиска сущности в методе действия.</span><span class="sxs-lookup"><span data-stu-id="1c347-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="1c347-231">Логика для выборки автора с использованием Entity Framework Core перемещается в связыватель модели.</span><span class="sxs-lookup"><span data-stu-id="1c347-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="1c347-232">Такой подход позволит существенно упростить работу, если у вас есть несколько методов для привязки к модели `Author`.</span><span class="sxs-lookup"><span data-stu-id="1c347-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="1c347-233">Чтобы указать определенный связыватель модели или имя только для данного типа или действия, можно применить атрибут `ModelBinder` к свойствам отдельной модели (таким как viewmodel) или к параметрам метода действия.</span><span class="sxs-lookup"><span data-stu-id="1c347-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="1c347-234">Реализация класса ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="1c347-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="1c347-235">Вместо применения атрибута можно реализовать класс `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="1c347-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="1c347-236">Таким образом реализуются встроенные связыватели платформы.</span><span class="sxs-lookup"><span data-stu-id="1c347-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="1c347-237">При указании типа, с которым работает связыватель, определяется тип создаваемого им аргумента, а **не** принимаемые им входные данные.</span><span class="sxs-lookup"><span data-stu-id="1c347-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="1c347-238">Следующий поставщик связывателей работает с `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="1c347-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="1c347-239">Если он добавляется в коллекцию поставщиков MVC, не нужно использовать атрибут `ModelBinder` в типизированных параметрах `Author` или `Author`.</span><span class="sxs-lookup"><span data-stu-id="1c347-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="1c347-240">Примечание. Приведенный выше код возвращает `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="1c347-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="1c347-241">`BinderTypeModelBinder` выступает в качестве фабрики для связывателей моделей и обеспечивает внедрение зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="1c347-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="1c347-242">Для доступа к EF Core классу `AuthorEntityBinder` требуется внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="1c347-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="1c347-243">Если связывателю модели необходимы службы из DI, используйте класс `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="1c347-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="1c347-244">Чтобы начать работу с настраиваемым поставщиком связывателей моделей, добавьте его в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1c347-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="1c347-245">При оценке связывателей моделей коллекция поставщиков проверяется в определенном порядке.</span><span class="sxs-lookup"><span data-stu-id="1c347-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="1c347-246">Используется первый поставщик, который возвращает связыватель.</span><span class="sxs-lookup"><span data-stu-id="1c347-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="1c347-247">Добавление поставщика в конец коллекции может привести к вызову встроенного связывателя модели раньше, чем будет вызван ваш собственный настраиваемый связыватель.</span><span class="sxs-lookup"><span data-stu-id="1c347-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="1c347-248">В этом примере настраиваемый поставщик добавляется в начало коллекции, чтобы использоваться для аргументов действия `Author`.</span><span class="sxs-lookup"><span data-stu-id="1c347-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="1c347-249">Полиморфная привязка моделей</span><span class="sxs-lookup"><span data-stu-id="1c347-249">Polymorphic model binding</span></span>

<span data-ttu-id="1c347-250">Привязка к разным моделям производных типов называется полиморфной привязкой модели.</span><span class="sxs-lookup"><span data-stu-id="1c347-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="1c347-251">Полиморфная пользовательская привязка модели требуется, когда значение запроса должно быть привязано к конкретному типу производной модели.</span><span class="sxs-lookup"><span data-stu-id="1c347-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="1c347-252">Характеристики полиморфной привязки моделей:</span><span class="sxs-lookup"><span data-stu-id="1c347-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="1c347-253">не является типичной для REST API, предназначенного для взаимодействия со всеми языками;</span><span class="sxs-lookup"><span data-stu-id="1c347-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="1c347-254">усложняет понимание того, как работают модели привязок.</span><span class="sxs-lookup"><span data-stu-id="1c347-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="1c347-255">Но если приложению требуется полиморфная привязка модели, реализация может выглядеть так, как в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="1c347-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="1c347-256">Рекомендации и советы</span><span class="sxs-lookup"><span data-stu-id="1c347-256">Recommendations and best practices</span></span>

<span data-ttu-id="1c347-257">Ниже приведены рекомендации относительно настраиваемых связывателей моделей.</span><span class="sxs-lookup"><span data-stu-id="1c347-257">Custom model binders:</span></span>

- <span data-ttu-id="1c347-258">Связыватели моделей не следует использовать для установки кодов состояния или возвращаемых результатов (например, "404 — не найдено").</span><span class="sxs-lookup"><span data-stu-id="1c347-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="1c347-259">При сбое привязки модели обрабатывать ошибку должен сам [фильтр действий](xref:mvc/controllers/filters) или логика в самом методе действия.</span><span class="sxs-lookup"><span data-stu-id="1c347-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="1c347-260">Связыватели наиболее полезны в сценариях исключения повторяющихся частей кода и решения взаимосвязанных проблем с методами действий.</span><span class="sxs-lookup"><span data-stu-id="1c347-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="1c347-261">Как правило, связыватели не следует использовать для преобразования строки в пользовательский тип. Лучшим вариантом обычно является <xref:System.ComponentModel.TypeConverter>.</span><span class="sxs-lookup"><span data-stu-id="1c347-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
