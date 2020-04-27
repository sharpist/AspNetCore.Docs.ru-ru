---
title: Глобализация и локализация в ASP.NET Core
author: rick-anderson
description: Сведения о службах и ПО промежуточного слоя, предоставляемых ASP.NET Core для локализации содержимого на разные языки и для разных региональных параметров.
ms.author: riande
ms.date: 11/30/2019
uid: fundamentals/localization
ms.openlocfilehash: 91db83eb8685c7bee5e976f386c2a12c4090b734
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440822"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="686c4-103">Глобализация и локализация в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="686c4-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="686c4-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Дэмиен Боуден](https://twitter.com/damien_bod) (Damien Bowden), [Барт Каликсто](https://twitter.com/bartmax) (Bart Calixto), [Надим Афана](https://afana.me/) (Nadeem Afana) и [Хишам Бин Атея](https://twitter.com/hishambinateya) (Hisham Bin Ateya)</span><span class="sxs-lookup"><span data-stu-id="686c4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="686c4-105">Создание многоязычного веб-сайта на основе ASP.NET Core позволит расширить аудиторию.</span><span class="sxs-lookup"><span data-stu-id="686c4-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="686c4-106">ASP.NET Core предоставляет службы и ПО промежуточного слоя для локализации на разные языки и для разных региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="686c4-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="686c4-107">Интернационализация предполагает [глобализацию](/dotnet/api/system.globalization) и [локализацию](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="686c4-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="686c4-108">Глобализация — это процесс разработки приложений, которые поддерживают разные языки и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="686c4-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="686c4-109">Глобализация добавляет поддержку ввода отображения и вывода определенного набора языковых сценариев, относящихся к конкретным регионам.</span><span class="sxs-lookup"><span data-stu-id="686c4-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="686c4-110">Локализация — это процесс адаптации глобализованного приложения, уже подготовленного к локализации, к определенному языку, языковому стандарту и региональным параметрам.</span><span class="sxs-lookup"><span data-stu-id="686c4-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="686c4-111">Дополнительные сведения см. в разделе **Термины, относящиеся к глобализации и локализации** ближе к концу этого документа.</span><span class="sxs-lookup"><span data-stu-id="686c4-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="686c4-112">Локализация приложения включает следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="686c4-112">App localization involves the following:</span></span>

1. <span data-ttu-id="686c4-113">обеспечение возможности локализации для содержимого приложения;</span><span class="sxs-lookup"><span data-stu-id="686c4-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="686c4-114">предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров;</span><span class="sxs-lookup"><span data-stu-id="686c4-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="686c4-115">реализацию стратегии по выбору языка и региональных параметров для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="686c4-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="686c4-116">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="686c4-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="686c4-117">Обеспечение возможности локализации для содержимого приложения</span><span class="sxs-lookup"><span data-stu-id="686c4-117">Make the app's content localizable</span></span>

<span data-ttu-id="686c4-118">Появившиеся в ASP.NET Core интерфейсы `IStringLocalizer` и `IStringLocalizer<T>` призваны повысить производительность при разработке локализованных приложений.</span><span class="sxs-lookup"><span data-stu-id="686c4-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="686c4-119">Интерфейс `IStringLocalizer` использует классы [ResourceManager](/dotnet/api/system.resources.resourcemanager) и [ResourceReader](/dotnet/api/system.resources.resourcereader) для предоставления ресурсов, связанных с определенным языком и региональными параметрами, во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="686c4-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="686c4-120">Этот простой интерфейс имеет индексатор и интерфейс `IEnumerable` для возврата локализованных строк.</span><span class="sxs-lookup"><span data-stu-id="686c4-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="686c4-121">`IStringLocalizer` не требует сохранять строки на языке по умолчанию в файле ресурсов.</span><span class="sxs-lookup"><span data-stu-id="686c4-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="686c4-122">Вы можете разрабатывать приложение, предназначенное для локализации, не создавая файлы ресурсов на ранних этапах разработки.</span><span class="sxs-lookup"><span data-stu-id="686c4-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="686c4-123">В приведенном ниже коде показано, как подготовить строку "About Title" для локализации.</span><span class="sxs-lookup"><span data-stu-id="686c4-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="686c4-124">В этом коде реализация `IStringLocalizer<T>` получена в результате [внедрения зависимостей](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="686c4-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="686c4-125">Если локализованное значение для строки "About Title" не найдено, возвращается ключ индексатора, то есть строка "About Title".</span><span class="sxs-lookup"><span data-stu-id="686c4-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="686c4-126">Вы можете оставить литеральные строки на языке по умолчанию и заключить их в средство локализации, чтобы сосредоточиться на разработке приложения.</span><span class="sxs-lookup"><span data-stu-id="686c4-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="686c4-127">Вы разрабатываете приложение на языке по умолчанию и подготавливаете его к локализации, не создавая предварительно файл ресурсов по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="686c4-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="686c4-128">Вы также можете выбрать традиционный подход и предоставить ключ для извлечения строки на языке по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="686c4-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="686c4-129">Для многих разработчиков новый рабочий процесс, который не требует наличия файла *RESX* на языке по умолчанию и позволяет просто заключать строки для дальнейшей обработки, может помочь снизить затраты на локализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="686c4-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="686c4-130">Другие разработчики могут предпочесть традиционный рабочий процесс, так как он упрощает работу с более длинными строками и обновление локализованных строк.</span><span class="sxs-lookup"><span data-stu-id="686c4-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="686c4-131">Используйте реализацию `IHtmlLocalizer<T>` для ресурсов, содержащих код HTML.</span><span class="sxs-lookup"><span data-stu-id="686c4-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="686c4-132">Интерфейс `IHtmlLocalizer` кодирует в HTML аргументы, отформатированные в строке ресурса, но не кодирует в HTML саму строку.</span><span class="sxs-lookup"><span data-stu-id="686c4-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="686c4-133">В выделенной ниже строке в HTML кодируется только значение параметра `name`.</span><span class="sxs-lookup"><span data-stu-id="686c4-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="686c4-134">**Примечание.** Как правило, требуется локализовать только текст, но не код HTML.</span><span class="sxs-lookup"><span data-stu-id="686c4-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="686c4-135">На самом нижнем уровне интерфейс `IStringLocalizerFactory` можно получить из [внедрения зависимостей](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="686c4-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="686c4-136">В приведенном выше коде демонстрируются оба фабричных метода создания.</span><span class="sxs-lookup"><span data-stu-id="686c4-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="686c4-137">Вы можете разбивать локализованные строки по контроллеру, области или использовать всего один конвейер.</span><span class="sxs-lookup"><span data-stu-id="686c4-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="686c4-138">В образце приложения для общих ресурсов применяется класс-заглушка с именем `SharedResource`.</span><span class="sxs-lookup"><span data-stu-id="686c4-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="686c4-139">Некоторые разработчики используют класс `Startup` для хранения глобальных или общих строк.</span><span class="sxs-lookup"><span data-stu-id="686c4-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="686c4-140">В примере ниже используются средства локализации `InfoController` и `SharedResource`.</span><span class="sxs-lookup"><span data-stu-id="686c4-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="686c4-141">Локализация представления</span><span class="sxs-lookup"><span data-stu-id="686c4-141">View localization</span></span>

<span data-ttu-id="686c4-142">Служба `IViewLocalizer` предоставляет локализованные строки для [представления](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="686c4-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="686c4-143">Класс `ViewLocalizer` реализует этот интерфейс и находит расположение ресурсов по пути к файлу представления.</span><span class="sxs-lookup"><span data-stu-id="686c4-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="686c4-144">В следующем примере кода демонстрируется использование реализации `IViewLocalizer` по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="686c4-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="686c4-145">Реализация `IViewLocalizer` по умолчанию находит файл ресурсов по имени файла представления.</span><span class="sxs-lookup"><span data-stu-id="686c4-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="686c4-146">Возможности использовать глобальный общий файл ресурсов нет.</span><span class="sxs-lookup"><span data-stu-id="686c4-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="686c4-147">`ViewLocalizer` реализует средство локализации с помощью интерфейса `IHtmlLocalizer`, поэтому Razor не кодирует локализованную строку в HTML.</span><span class="sxs-lookup"><span data-stu-id="686c4-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="686c4-148">Вы можете параметризовать строки ресурсов, и `IViewLocalizer` будет кодировать в HTML параметры, но не строки ресурсов.</span><span class="sxs-lookup"><span data-stu-id="686c4-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="686c4-149">Рассмотрим следующую разметку Razor:</span><span class="sxs-lookup"><span data-stu-id="686c4-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="686c4-150">Файл ресурсов на французском языке может содержать следующие ресурсы:</span><span class="sxs-lookup"><span data-stu-id="686c4-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="686c4-151">Ключ</span><span class="sxs-lookup"><span data-stu-id="686c4-151">Key</span></span> | <span data-ttu-id="686c4-152">Значение</span><span class="sxs-lookup"><span data-stu-id="686c4-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="686c4-153">Преобразованное для просмотра представление будет содержать разметку HTML из файла ресурсов.</span><span class="sxs-lookup"><span data-stu-id="686c4-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="686c4-154">**Примечание.** Как правило, требуется локализовать только текст, но не код HTML.</span><span class="sxs-lookup"><span data-stu-id="686c4-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="686c4-155">Чтобы использовать в представлении общий файл ресурсов, внедрите `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="686c4-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="686c4-156">Локализация DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="686c4-156">DataAnnotations localization</span></span>

<span data-ttu-id="686c4-157">Сообщения об ошибках DataAnnotations локализуются с помощью `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="686c4-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="686c4-158">При использовании параметра `ResourcesPath = "Resources"` сообщения об ошибках в `RegisterViewModel` могут сохраняться по одному из следующих путей:</span><span class="sxs-lookup"><span data-stu-id="686c4-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="686c4-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="686c4-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="686c4-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="686c4-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="686c4-161">В ASP.NET Core MVC 1.1.0 и более поздних версиях атрибуты, не относящиеся в проверке, локализуются.</span><span class="sxs-lookup"><span data-stu-id="686c4-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="686c4-162">В ASP.NET Core MVC 1.0 **не** производится поиск локализованных строк для атрибутов, не относящихся к проверке.</span><span class="sxs-lookup"><span data-stu-id="686c4-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="686c4-163">Использование одной строки ресурса для нескольких классов</span><span class="sxs-lookup"><span data-stu-id="686c4-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="686c4-164">В следующем коде показано, как использовать одну строку ресурса для атрибутов проверки с несколькими классами:</span><span class="sxs-lookup"><span data-stu-id="686c4-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="686c4-165">В предыдущем коде `SharedResource` — это класс, соответствующий файлу RESX, в котором хранятся сообщения о проверке.</span><span class="sxs-lookup"><span data-stu-id="686c4-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="686c4-166">При таком подходе DataAnnotations будут использовать только `SharedResource`, а не ресурс для каждого класса.</span><span class="sxs-lookup"><span data-stu-id="686c4-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="686c4-167">Предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="686c4-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="686c4-168">SupportedCultures и SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="686c4-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="686c4-169">ASP.NET Core позволяет указывать два значения языка и региональных параметров: `SupportedCultures` и `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="686c4-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="686c4-170">Объект [CultureInfo](/dotnet/api/system.globalization.cultureinfo) для `SupportedCultures` определяет результаты функций, зависящих от языка и региональных параметров: форматирование дат, времени, чисел, денежных единиц и т. д.</span><span class="sxs-lookup"><span data-stu-id="686c4-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="686c4-171">`SupportedCultures` также определяет порядок сортировки текста, соглашения о регистре символов и способы сравнения строк.</span><span class="sxs-lookup"><span data-stu-id="686c4-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="686c4-172">Дополнительные сведения о получении сервером значения языка и региональных параметров см. в описании свойства [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="686c4-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="686c4-173">Значение `SupportedUICultures` определяет то, какие строки переводов (в файле *RESX*) ищет объект [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="686c4-173">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="686c4-174">`ResourceManager` ищет связанные с языком и региональными параметрами строки, которые определяются значением `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="686c4-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="686c4-175">Каждый поток в .NET имеет объекты `CurrentCulture` и `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="686c4-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="686c4-176">ASP.NET Core проверяет эти значения при обработке функций, зависящих от языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="686c4-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="686c4-177">Например, если для текущего потока заданы язык и региональные параметры "en-US" (английский, США), метод `DateTime.Now.ToLongDateString()` выводит строку "Thursday, February 18, 2016", но если `CurrentCulture` имеет значение "ru-RU" (русский, Россия), выводится строка "четверг, 18 февраля 2016 г."</span><span class="sxs-lookup"><span data-stu-id="686c4-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="686c4-178">Файлы ресурсов</span><span class="sxs-lookup"><span data-stu-id="686c4-178">Resource files</span></span>

<span data-ttu-id="686c4-179">Файл ресурсов — это полезное средство для отделения локализуемых строк от кода.</span><span class="sxs-lookup"><span data-stu-id="686c4-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="686c4-180">Переведенные строки на языках, отличных от языка по умолчанию, содержатся в отдельных файлах ресурсов с расширением *RESX*.</span><span class="sxs-lookup"><span data-stu-id="686c4-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="686c4-181">Например, вам нужно создать файл ресурсов для испанского языка с именем *Welcome.es.resx*, который будет содержать переведенные строки.</span><span class="sxs-lookup"><span data-stu-id="686c4-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="686c4-182">"es" — это код испанского языка.</span><span class="sxs-lookup"><span data-stu-id="686c4-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="686c4-183">Чтобы создать этот файл ресурсов в Visual Studio, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="686c4-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="686c4-184">В **обозревателе решений** щелкните правой кнопкой мыши папку, которая будет содержать файл ресурсов, а затем выберите пункты **Добавить** > **Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="686c4-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Вложенное контекстное меню: в обозревателе решений контекстное меню открывается для папки ресурсов.](localization/_static/newi.png)

2. <span data-ttu-id="686c4-187">В поле **Поиск установленных шаблонов** введите слово "ресурс" и укажите имя файла.</span><span class="sxs-lookup"><span data-stu-id="686c4-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Диалоговое окно ''Добавление нового элемента''](localization/_static/res.png)

3. <span data-ttu-id="686c4-189">Введите значение ключа (строку на исходном языке) в столбце **Имя** и переведенную строку в столбце **Значение**.</span><span class="sxs-lookup"><span data-stu-id="686c4-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Файл Welcome.es.resx (файлов ресурсов с приветствиями на испанском языке) со словом "Hello" в столбце "Имя" и словом "Hol"a ("Hello" на испанском) в столбце "Значение"](localization/_static/hola.png)

    <span data-ttu-id="686c4-191">В Visual Studio отобразится файл *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="686c4-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Обозреватель решений с файлом ресурсов на испанском языке](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="686c4-193">Именование файлов ресурсов</span><span class="sxs-lookup"><span data-stu-id="686c4-193">Resource file naming</span></span>

<span data-ttu-id="686c4-194">Имена ресурсов представляют собой полные имена типов соответствующего класса за исключением имени сборки.</span><span class="sxs-lookup"><span data-stu-id="686c4-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="686c4-195">Например, ресурс на французском языке в проекте, главная сборка которого имеет имя `LocalizationWebsite.Web.dll`, для класса `LocalizationWebsite.Web.Startup` будет иметь имя *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="686c4-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="686c4-196">Ресурс для класса `LocalizationWebsite.Web.Controllers.HomeController` будет иметь имя *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="686c4-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="686c4-197">Если пространство имен целевого класса не совпадает с именем сборки, необходимо использовать полное имя типа.</span><span class="sxs-lookup"><span data-stu-id="686c4-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="686c4-198">Например, в образце проекта ресурс для типа `ExtraNamespace.Tools` будет иметь имя *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="686c4-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="686c4-199">В образце проекта метод `ConfigureServices` присваивает свойству `ResourcesPath` значение "Resources", поэтому относительный путь к файлу ресурсов на французском языке для контроллера домашней страницы в проекте будет иметь вид *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="686c4-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="686c4-200">Кроме того, для упорядочения файлов ресурсов можно использовать папки.</span><span class="sxs-lookup"><span data-stu-id="686c4-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="686c4-201">Для контроллера домашней страницы путь будет иметь вид *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="686c4-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="686c4-202">Если параметр `ResourcesPath` не используется, файл *RESX* будет находиться в базовом каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="686c4-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="686c4-203">Файл ресурса для `HomeController` будет иметь имя *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="686c4-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="686c4-204">Выбор соглашения об именовании на основе точечной нотации или пути зависит от того, как следует упорядочивать файлы ресурсов.</span><span class="sxs-lookup"><span data-stu-id="686c4-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="686c4-205">Имя ресурса</span><span class="sxs-lookup"><span data-stu-id="686c4-205">Resource name</span></span> | <span data-ttu-id="686c4-206">Точечная нотация или путь</span><span class="sxs-lookup"><span data-stu-id="686c4-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="686c4-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="686c4-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="686c4-208">Точки</span><span class="sxs-lookup"><span data-stu-id="686c4-208">Dot</span></span>  |
| <span data-ttu-id="686c4-209">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="686c4-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="686c4-210">Path</span><span class="sxs-lookup"><span data-stu-id="686c4-210">Path</span></span> |
|    |     |

<span data-ttu-id="686c4-211">Файлы ресурсов, для которых используется директива `@inject IViewLocalizer` в представлениях Razor, следуют той же модели.</span><span class="sxs-lookup"><span data-stu-id="686c4-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="686c4-212">Файлу ресурсов для представления может присваиваться имя на основе либо точечной нотации, либо пути.</span><span class="sxs-lookup"><span data-stu-id="686c4-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="686c4-213">В файле ресурсов для представления Razor имитируется путь к связанному файлу представления.</span><span class="sxs-lookup"><span data-stu-id="686c4-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="686c4-214">Допустим, свойству `ResourcesPath` присвоено значение "Resources". В этом случае файл ресурсов на французском языке, связанный с представлением *Views/Home/About.cshtml*, может иметь одно из следующих имен:</span><span class="sxs-lookup"><span data-stu-id="686c4-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="686c4-215">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="686c4-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="686c4-216">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="686c4-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="686c4-217">Если параметр `ResourcesPath` не используется, файл *RESX* для представления будет находиться в той же папке, что и представление.</span><span class="sxs-lookup"><span data-stu-id="686c4-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="686c4-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="686c4-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="686c4-219">Атрибут [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) содержит корневое пространство имен сборки, если корневое пространство имен сборки отличается от имени сборки.</span><span class="sxs-lookup"><span data-stu-id="686c4-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="686c4-220">Это может произойти, если имя проекта — недопустимый идентификатор .NET.</span><span class="sxs-lookup"><span data-stu-id="686c4-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="686c4-221">Например, `my-project-name.csproj` будет использовать корневое пространство имен `my_project_name` и имя сборки `my-project-name`, что повлечет эту ошибку.</span><span class="sxs-lookup"><span data-stu-id="686c4-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="686c4-222">Если корневое пространство имен сборки отличается от имени сборки</span><span class="sxs-lookup"><span data-stu-id="686c4-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="686c4-223">Локализация не работает по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="686c4-223">Localization does not work by default.</span></span>
* <span data-ttu-id="686c4-224">Локализация завершается сбоем из-за метода поиска ресурсов в сборке.</span><span class="sxs-lookup"><span data-stu-id="686c4-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="686c4-225">`RootNamespace` — это значение во время сборки, которое недоступно выполняющемуся процессу.</span><span class="sxs-lookup"><span data-stu-id="686c4-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="686c4-226">Если `RootNamespace` отличается от `AssemblyName`, включите следующее в файл *AssemblyInfo.cs* (со значениями параметров, замененными фактическими значениями).</span><span class="sxs-lookup"><span data-stu-id="686c4-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="686c4-227">Приведенный выше код обеспечивает успешное разрешение RESX-файлов.</span><span class="sxs-lookup"><span data-stu-id="686c4-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="686c4-228">Резервный язык и региональные параметры</span><span class="sxs-lookup"><span data-stu-id="686c4-228">Culture fallback behavior</span></span>

<span data-ttu-id="686c4-229">При поиске ресурса локализации использует резервный язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="686c4-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="686c4-230">Если запрошенный язык и региональные параметры не найдены, используется родительский язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="686c4-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="686c4-231">Кстати, родительский язык и региональные параметры представляют свойство [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent).</span><span class="sxs-lookup"><span data-stu-id="686c4-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="686c4-232">Обычно (но не всегда) это означает удаление национального символа из ISO.</span><span class="sxs-lookup"><span data-stu-id="686c4-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="686c4-233">Например, диалект испанского в Мексике — es-MX.</span><span class="sxs-lookup"><span data-stu-id="686c4-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="686c4-234">Родительский элемент "es" (испанский) не относится к той или иной стране.</span><span class="sxs-lookup"><span data-stu-id="686c4-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="686c4-235">Допустим, сайт получает запрос на ресурс "Приветствие" с использованием языка и региональных параметров fr-CA.</span><span class="sxs-lookup"><span data-stu-id="686c4-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="686c4-236">Система локализации ищет в следующих ресурсах по порядку и выбирает первое совпадение:</span><span class="sxs-lookup"><span data-stu-id="686c4-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="686c4-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="686c4-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="686c4-238">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="686c4-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="686c4-239">*Welcome.resx* (если `NeutralResourcesLanguage` — fr-CA)</span><span class="sxs-lookup"><span data-stu-id="686c4-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="686c4-240">Если вы удалите указатель языка и региональных параметров ".fr" и при этом задан французский язык и региональные параметры, будет считан файл ресурсов по умолчанию и строки локализуются.</span><span class="sxs-lookup"><span data-stu-id="686c4-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="686c4-241">Диспетчер ресурсов назначает ресурс по умолчанию или резервный ресурс на тот случай, когда соответствия запрошенному языку и региональным параметрам не найдено.</span><span class="sxs-lookup"><span data-stu-id="686c4-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="686c4-242">Если нужно, чтобы при отсутствии ресурса для запрошенного языка и региональных параметров просто возвращался ключ, не используйте файл ресурсов по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="686c4-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="686c4-243">Создание файлов ресурсов с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="686c4-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="686c4-244">Если вы создаете в Visual Studio файл ресурсов, в имени которого не указаны язык и региональные параметры (например, *Welcome.resx*), Visual Studio создаст класс C# со свойством для каждой строки.</span><span class="sxs-lookup"><span data-stu-id="686c4-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="686c4-245">В ASP.NET Core обычно требуется иное поведение.</span><span class="sxs-lookup"><span data-stu-id="686c4-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="686c4-246">Как правило, файл ресурсов *RESX* по умолчанию (файл *RESX* без указания языка и региональных параметров) не используется.</span><span class="sxs-lookup"><span data-stu-id="686c4-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="686c4-247">Мы рекомендуем создать файл *RESX* с указанием языка и региональных параметров (например, *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="686c4-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="686c4-248">При создании файла *RESX* с указанием языка и региональных параметров среда Visual Studio не создает файл класса.</span><span class="sxs-lookup"><span data-stu-id="686c4-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="686c4-249">Добавление других языков и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="686c4-249">Add other cultures</span></span>

<span data-ttu-id="686c4-250">Каждое сочетание языка и региональных параметров (кроме языка по умолчанию) требует уникального файла ресурсов.</span><span class="sxs-lookup"><span data-stu-id="686c4-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="686c4-251">Создавая файлы ресурсов для разных языков, языковых стандартов и региональных параметров, вы включаете в их имена коды языков ISO (например, **en-us**, **ru-ru** и **fr-ca**).</span><span class="sxs-lookup"><span data-stu-id="686c4-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="686c4-252">Эти коды ISO помещаются между именем файла и расширением *RESX*, например *Welcome.es-MX.resx* (испанский, Мексика).</span><span class="sxs-lookup"><span data-stu-id="686c4-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="686c4-253">реализацию стратегии по выбору языка и региональных параметров для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="686c4-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="686c4-254">Настройка локализации</span><span class="sxs-lookup"><span data-stu-id="686c4-254">Configure localization</span></span>

<span data-ttu-id="686c4-255">Локализация настраивается в методе `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="686c4-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="686c4-256">Метод `AddLocalization` добавляет службы локализации в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="686c4-256">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="686c4-257">В приведенном выше коде также задается путь к ресурсам "Resources".</span><span class="sxs-lookup"><span data-stu-id="686c4-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="686c4-258">Метод `AddViewLocalization` добавляет поддержку файлов локализованных представлений.</span><span class="sxs-lookup"><span data-stu-id="686c4-258">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="686c4-259">В этом примере локализация образца представления основана на суффиксе файла представления,</span><span class="sxs-lookup"><span data-stu-id="686c4-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="686c4-260">например "fr" в файле *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="686c4-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="686c4-261">Метод `AddDataAnnotationsLocalization` добавляет поддержку локализованных сообщений проверки `DataAnnotations` посредством абстракций `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="686c4-261">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="686c4-262">Промежуточный слой локализации</span><span class="sxs-lookup"><span data-stu-id="686c4-262">Localization middleware</span></span>

<span data-ttu-id="686c4-263">Текущий язык и региональные параметры в запросе задаются в [промежуточном слое](xref:fundamentals/middleware/index) локализации.</span><span class="sxs-lookup"><span data-stu-id="686c4-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="686c4-264">Промежуточный слой локализации включается в методе `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="686c4-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="686c4-265">Промежуточный слой локализации должен настраиваться перед другими промежуточными слоями, которые могут проверять язык и региональные параметры запроса (например, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="686c4-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="686c4-266">`UseRequestLocalization` инициализирует объект `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="686c4-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="686c4-267">При каждом запросе проверяется список поставщиков `RequestCultureProvider` в объекте `RequestLocalizationOptions` и используется первый поставщик, который может успешно определить язык и региональные параметры запроса.</span><span class="sxs-lookup"><span data-stu-id="686c4-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="686c4-268">Поставщики по умолчанию берутся из класса `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="686c4-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="686c4-269">В списке по умолчанию поставщики следуют от наиболее конкретных до наиболее общих.</span><span class="sxs-lookup"><span data-stu-id="686c4-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="686c4-270">Далее в этой статье вы узнаете, как можно изменить этот порядок и даже добавить пользовательский поставщик языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="686c4-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="686c4-271">Если ни один из поставщиков не может определить язык и региональные параметры запроса, используется `DefaultRequestCulture`.</span><span class="sxs-lookup"><span data-stu-id="686c4-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="686c4-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="686c4-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="686c4-273">Некоторые приложения используют строку запроса для указания [языка и региональных параметров самого приложения и пользовательского интерфейса](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="686c4-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="686c4-274">Если в приложении для этого применяется файл cookie или заголовок Accept-Language, добавление строки запроса к URL-адресу может быть полезным в целях отладки и тестирования кода.</span><span class="sxs-lookup"><span data-stu-id="686c4-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="686c4-275">По умолчанию поставщик `QueryStringRequestCultureProvider` регистрируется в качестве первого поставщика локализации в списке `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="686c4-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="686c4-276">Вы передаете параметры строки запроса `culture` и `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="686c4-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="686c4-277">В следующем примере в качестве языка и региональных параметров (язык и регион) задаются испанский язык и Мексика:</span><span class="sxs-lookup"><span data-stu-id="686c4-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="686c4-278">Если передан только один из двух параметров (`culture` или `ui-culture`), поставщик строки запроса задаст с его помощью оба значения.</span><span class="sxs-lookup"><span data-stu-id="686c4-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="686c4-279">Например, если передан только язык и региональные параметры, будут заданы оба значения `Culture` и `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="686c4-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="686c4-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="686c4-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="686c4-281">Рабочие приложения часто предоставляют механизм для указания языка и региональных параметров с помощью соответствующего файла cookie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="686c4-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="686c4-282">Чтобы создать файл cookie, используйте метод `MakeCookieValue`.</span><span class="sxs-lookup"><span data-stu-id="686c4-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="686c4-283">`CookieRequestCultureProvider` `DefaultCookieName` возвращает имя файла cookie по умолчанию, с помощью которого отслеживается предпочтительный язык и региональные параметры пользователя.</span><span class="sxs-lookup"><span data-stu-id="686c4-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="686c4-284">Имя файла cookie по умолчанию — `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="686c4-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="686c4-285">Файл cookie имеет формат `c=%LANGCODE%|uic=%LANGCODE%`, где `c` — это `Culture`, а `uic` — это `UICulture`, например:</span><span class="sxs-lookup"><span data-stu-id="686c4-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="686c4-286">Если указаны либо только язык и региональные параметры, либо только язык и региональные параметры пользовательского интерфейса, это значение будет использоваться для обоих параметров.</span><span class="sxs-lookup"><span data-stu-id="686c4-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="686c4-287">Заголовок HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="686c4-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="686c4-288">[Заголовок Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) может задаваться в большинстве браузеров и изначально предназначался для указания языка пользователя.</span><span class="sxs-lookup"><span data-stu-id="686c4-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="686c4-289">Он показывает, какой язык был выбран в браузере или унаследован от базовой операционной системы.</span><span class="sxs-lookup"><span data-stu-id="686c4-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="686c4-290">Заголовок HTTP Accept-Language в запросе из браузера — это не самый надежный способ определения предпочтительного языка пользователя (см. статью [Настройка языковых предпочтений в браузере](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="686c4-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="686c4-291">В рабочем приложении должна быть возможность выбора языка и региональных параметров пользователем.</span><span class="sxs-lookup"><span data-stu-id="686c4-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="686c4-292">Задание заголовка HTTP Accept-Language в Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="686c4-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="686c4-293">Щелкните значок шестеренки и выберите пункт **Свойства браузера**.</span><span class="sxs-lookup"><span data-stu-id="686c4-293">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="686c4-294">Нажмите кнопку **Языки**.</span><span class="sxs-lookup"><span data-stu-id="686c4-294">Tap **Languages**.</span></span>

    ![Свойства браузера](localization/_static/lang.png)

3. <span data-ttu-id="686c4-296">Нажмите **Задать предпочитаемые языки**.</span><span class="sxs-lookup"><span data-stu-id="686c4-296">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="686c4-297">Нажмите **Добавить язык**.</span><span class="sxs-lookup"><span data-stu-id="686c4-297">Tap **Add a language**.</span></span>

5. <span data-ttu-id="686c4-298">Добавьте язык.</span><span class="sxs-lookup"><span data-stu-id="686c4-298">Add the language.</span></span>

6. <span data-ttu-id="686c4-299">Выберите язык, а затем нажмите кнопку **Вверх**.</span><span class="sxs-lookup"><span data-stu-id="686c4-299">Tap the language, then tap **Move Up**.</span></span>

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a><span data-ttu-id="686c4-300">Заголовок HTTP Content-Language</span><span class="sxs-lookup"><span data-stu-id="686c4-300">The Content-Language HTTP header</span></span>

<span data-ttu-id="686c4-301">Заголовок сущности [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language):</span><span class="sxs-lookup"><span data-stu-id="686c4-301">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="686c4-302">Используется для описания языков, предназначенных для аудитории.</span><span class="sxs-lookup"><span data-stu-id="686c4-302">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="686c4-303">Позволяет пользователю различать в соответствии с предпочтительным языком пользователя.</span><span class="sxs-lookup"><span data-stu-id="686c4-303">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="686c4-304">Заголовки сущностей используются как в HTTP-запросах, так и в ответах.</span><span class="sxs-lookup"><span data-stu-id="686c4-304">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="686c4-305">Заголовок `Content-Language` можно добавить, задав свойство `ApplyCurrentCultureToResponseHeaders`.</span><span class="sxs-lookup"><span data-stu-id="686c4-305">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="686c4-306">Добавление заголовка `Content-Language`:</span><span class="sxs-lookup"><span data-stu-id="686c4-306">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="686c4-307">Позволяет RequestLocalizationMiddleware задать заголовок `Content-Language` с `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="686c4-307">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="686c4-308">Устраняет необходимость явной установки заголовка ответа `Content-Language`.</span><span class="sxs-lookup"><span data-stu-id="686c4-308">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a><span data-ttu-id="686c4-309">Использование пользовательского поставщика</span><span class="sxs-lookup"><span data-stu-id="686c4-309">Use a custom provider</span></span>

<span data-ttu-id="686c4-310">Предположим, необходимо, чтобы ваши клиенты могли сохранять информацию о своем языке и региональных параметрах в ваших базах данных.</span><span class="sxs-lookup"><span data-stu-id="686c4-310">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="686c4-311">Можно написать поставщик, который будет искать эти значения для пользователя.</span><span class="sxs-lookup"><span data-stu-id="686c4-311">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="686c4-312">В следующем примере кода показано, как добавить пользовательский поставщик:</span><span class="sxs-lookup"><span data-stu-id="686c4-312">The following code shows how to add a custom provider:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

<span data-ttu-id="686c4-313">Для добавления или удаления поставщиков локализации используйте объект `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="686c4-313">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="686c4-314">Задание языка и региональных параметров программным образом</span><span class="sxs-lookup"><span data-stu-id="686c4-314">Set the culture programmatically</span></span>

<span data-ttu-id="686c4-315">В образце проекта **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть пользовательский интерфейс для задания значения `Culture`.</span><span class="sxs-lookup"><span data-stu-id="686c4-315">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="686c4-316">Файл *Views/Shared/_SelectLanguagePartial.cshtml* позволяет выбрать язык и региональные параметры из списка поддерживаемых:</span><span class="sxs-lookup"><span data-stu-id="686c4-316">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="686c4-317">Файл *Views/Shared/_SelectLanguagePartial.cshtml* добавляется в раздел `footer` файла макета, поэтому он доступен всем представлениям:</span><span class="sxs-lookup"><span data-stu-id="686c4-317">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="686c4-318">Метод `SetLanguage` задает файл cookie языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="686c4-318">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="686c4-319">Подключить файл *_SelectLanguagePartial.cshtml* к образцу кода этого проекта нельзя.</span><span class="sxs-lookup"><span data-stu-id="686c4-319">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="686c4-320">В проекте **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть код для передачи объекта `RequestLocalizationOptions` в частичное представление Razor посредством контейнера [внедрения зависимостей](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="686c4-320">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="686c4-321">Данные маршрутов привязки модели и строки запросов</span><span class="sxs-lookup"><span data-stu-id="686c4-321">Model binding route data and query strings</span></span>

<span data-ttu-id="686c4-322">См. раздел [Поведение глобализации для данных маршрутов привязки модели и строк запросов](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="686c4-322">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="686c4-323">Термины, относящиеся к глобализации и локализации</span><span class="sxs-lookup"><span data-stu-id="686c4-323">Globalization and localization terms</span></span>

<span data-ttu-id="686c4-324">Процесс локализации приложения требует базового понимания распространенных кодировок, часто используемых при разработке современного программного обеспечения, и связанных с ними проблем.</span><span class="sxs-lookup"><span data-stu-id="686c4-324">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="686c4-325">Хотя на всех компьютерах текст сохраняется в виде цифр (кодов), в разных системах эти коды для одного и того же текста различаются.</span><span class="sxs-lookup"><span data-stu-id="686c4-325">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="686c4-326">Под процессом локализации понимается перевод пользовательского интерфейса приложения для определенного языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="686c4-326">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="686c4-327">[Возможность локализации](/dotnet/standard/globalization-localization/localizability-review) — это определяемое на промежуточной стадии состояние готовности глобализованного приложения к локализации.</span><span class="sxs-lookup"><span data-stu-id="686c4-327">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="686c4-328">Формат [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) для названия языка и региональных параметров имеет вид `<languagecode2>-<country/regioncode2>`, где `<languagecode2>` — это код языка, а `<country/regioncode2>` — код субкультуры.</span><span class="sxs-lookup"><span data-stu-id="686c4-328">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="686c4-329">Примеры: `es-CL` для испанского языка (Чили), `en-US` для английского языка (США), `en-AU` для английского языка (Австралия).</span><span class="sxs-lookup"><span data-stu-id="686c4-329">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="686c4-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) — это комбинация двухбуквенного кода культуры ISO 639 в нижнем регистре (он связан с языком) и двухбуквенного кода субкультуры ISO 3166 в верхнем регистре (он связан со страной или регионом).</span><span class="sxs-lookup"><span data-stu-id="686c4-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="686c4-331">См. статью [Имя языка и региональных параметров](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="686c4-331">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="686c4-332">Вместо слова "интернационализация" (internationalization) часто используют аббревиатуру "I18N".</span><span class="sxs-lookup"><span data-stu-id="686c4-332">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="686c4-333">В аббревиатуру включаются первая и последняя буквы слова, а также число букв между ними, то есть 18 — это число букв между первой ("I") и последней ("N") буквами.</span><span class="sxs-lookup"><span data-stu-id="686c4-333">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="686c4-334">То же самое касается глобализации (Globalization — G11N) и локализации (Localization — L10N).</span><span class="sxs-lookup"><span data-stu-id="686c4-334">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="686c4-335">Термины:</span><span class="sxs-lookup"><span data-stu-id="686c4-335">Terms:</span></span>

* <span data-ttu-id="686c4-336">Глобализация (G11N): процесс подготовки приложения к поддержке различных языков и регионов.</span><span class="sxs-lookup"><span data-stu-id="686c4-336">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="686c4-337">Локализация (L10N): процесс настройки приложения для работы с конкретным языком и регионом.</span><span class="sxs-lookup"><span data-stu-id="686c4-337">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="686c4-338">Интернационализация (I18N): описывает глобализацию и локализацию.</span><span class="sxs-lookup"><span data-stu-id="686c4-338">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="686c4-339">Язык и региональные параметры: обозначает язык и (необязательно) регион.</span><span class="sxs-lookup"><span data-stu-id="686c4-339">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="686c4-340">Нейтральный язык и региональные параметры: язык и региональные параметры, для которых указан язык, но не регион.</span><span class="sxs-lookup"><span data-stu-id="686c4-340">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="686c4-341">(например, "en", "es").</span><span class="sxs-lookup"><span data-stu-id="686c4-341">(for example "en", "es")</span></span>
* <span data-ttu-id="686c4-342">Конкретный язык и региональные параметры: язык и региональные параметры, для которых указаны язык и регион.</span><span class="sxs-lookup"><span data-stu-id="686c4-342">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="686c4-343">(например, "en-US", "en-GB", "es-CL").</span><span class="sxs-lookup"><span data-stu-id="686c4-343">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="686c4-344">Родительский язык и региональные параметры: нейтральные язык и региональные параметры, содержащие конкретные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="686c4-344">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="686c4-345">(например, "en" — это родительские язык и региональные параметры для "en-US" и "en-GB").</span><span class="sxs-lookup"><span data-stu-id="686c4-345">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="686c4-346">Языковой стандарт: тот же, что и язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="686c4-346">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="686c4-347">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="686c4-347">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="686c4-348">[Проект Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb), используемый в этой статье.</span><span class="sxs-lookup"><span data-stu-id="686c4-348">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="686c4-349">Глобализация и локализация приложений .NET</span><span class="sxs-lookup"><span data-stu-id="686c4-349">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="686c4-350">Ресурсы в RESX-файлах</span><span class="sxs-lookup"><span data-stu-id="686c4-350">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="686c4-351">Набор средств многоязычных приложений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="686c4-351">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="686c4-352">Локализация и универсальные шаблоны</span><span class="sxs-lookup"><span data-stu-id="686c4-352">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)
