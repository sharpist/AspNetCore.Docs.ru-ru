---
<span data-ttu-id="39277-101">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-102">'Blazor'</span></span>
- <span data-ttu-id="39277-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-103">'Identity'</span></span>
- <span data-ttu-id="39277-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-105">'Razor'</span></span>
- <span data-ttu-id="39277-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-106">'SignalR' uid:</span></span> 

---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="39277-107">Глобализация и локализация в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="39277-107">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="39277-108">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Дэмиен Боуден](https://twitter.com/damien_bod) (Damien Bowden), [Барт Каликсто](https://twitter.com/bartmax) (Bart Calixto), [Надим Афана](https://afana.me/) (Nadeem Afana) и [Хишам Бин Атея](https://twitter.com/hishambinateya) (Hisham Bin Ateya)</span><span class="sxs-lookup"><span data-stu-id="39277-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="39277-109">Создание многоязычного веб-сайта позволит расширить аудиторию.</span><span class="sxs-lookup"><span data-stu-id="39277-109">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="39277-110">ASP.NET Core предоставляет службы и ПО промежуточного слоя для локализации на разные языки и для разных региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-110">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="39277-111">Интернационализация предполагает [глобализацию](/dotnet/api/system.globalization) и [локализацию](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="39277-111">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="39277-112">Глобализация — это процесс разработки приложений, которые поддерживают разные языки и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-112">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="39277-113">Глобализация добавляет поддержку ввода отображения и вывода определенного набора языковых сценариев, относящихся к конкретным регионам.</span><span class="sxs-lookup"><span data-stu-id="39277-113">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="39277-114">Локализация — это процесс адаптации глобализованного приложения, уже подготовленного к локализации, к определенному языку, языковому стандарту и региональным параметрам.</span><span class="sxs-lookup"><span data-stu-id="39277-114">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="39277-115">Дополнительные сведения см. в разделе **Термины, относящиеся к глобализации и локализации** ближе к концу этого документа.</span><span class="sxs-lookup"><span data-stu-id="39277-115">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="39277-116">Локализация приложения включает следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="39277-116">App localization involves the following:</span></span>

1. <span data-ttu-id="39277-117">обеспечение возможности локализации для содержимого приложения;</span><span class="sxs-lookup"><span data-stu-id="39277-117">Make the app's content localizable</span></span>
1. <span data-ttu-id="39277-118">предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров;</span><span class="sxs-lookup"><span data-stu-id="39277-118">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="39277-119">реализацию стратегии по выбору языка и региональных параметров для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="39277-119">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="39277-120">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="39277-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="39277-121">Обеспечение возможности локализации для содержимого приложения</span><span class="sxs-lookup"><span data-stu-id="39277-121">Make the app's content localizable</span></span>

<span data-ttu-id="39277-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` не требует сохранять строки на языке по умолчанию в файле ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="39277-123">Вы можете разрабатывать приложение, предназначенное для локализации, не создавая файлы ресурсов на ранних этапах разработки.</span><span class="sxs-lookup"><span data-stu-id="39277-123">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="39277-124">В приведенном ниже коде показано, как подготовить строку "About Title" для локализации.</span><span class="sxs-lookup"><span data-stu-id="39277-124">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="39277-125">В предыдущем коде реализация `IStringLocalizer<T>` получена в результате [внедрения зависимостей](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="39277-125">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="39277-126">Если локализованное значение для строки "About Title" не найдено, возвращается ключ индексатора, то есть строка "About Title".</span><span class="sxs-lookup"><span data-stu-id="39277-126">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="39277-127">Вы можете оставить литеральные строки на языке по умолчанию и заключить их в средство локализации, чтобы сосредоточиться на разработке приложения.</span><span class="sxs-lookup"><span data-stu-id="39277-127">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="39277-128">Вы разрабатываете приложение на языке по умолчанию и подготавливаете его к локализации, не создавая предварительно файл ресурсов по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39277-128">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="39277-129">Вы также можете выбрать традиционный подход и предоставить ключ для извлечения строки на языке по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39277-129">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="39277-130">Для многих разработчиков новый рабочий процесс, который не требует наличия файла *RESX* на языке по умолчанию и позволяет просто заключать строки для дальнейшей обработки, может помочь снизить затраты на локализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="39277-130">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="39277-131">Другие разработчики могут предпочесть традиционный рабочий процесс, так как он упрощает работу с более длинными строками и обновление локализованных строк.</span><span class="sxs-lookup"><span data-stu-id="39277-131">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="39277-132">Используйте реализацию `IHtmlLocalizer<T>` для ресурсов, содержащих код HTML.</span><span class="sxs-lookup"><span data-stu-id="39277-132">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="39277-133">Интерфейс `IHtmlLocalizer` кодирует в HTML аргументы, отформатированные в строке ресурса, но не кодирует в HTML саму строку.</span><span class="sxs-lookup"><span data-stu-id="39277-133">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="39277-134">В выделенной ниже строке в HTML кодируется только значение параметра `name`.</span><span class="sxs-lookup"><span data-stu-id="39277-134">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="39277-135">**Примечание.** Как правило, требуется локализовать только текст, но не код HTML.</span><span class="sxs-lookup"><span data-stu-id="39277-135">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="39277-136">На самом нижнем уровне интерфейс `IStringLocalizerFactory` можно получить из [внедрения зависимостей](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="39277-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="39277-137">В приведенном выше коде демонстрируются оба фабричных метода создания.</span><span class="sxs-lookup"><span data-stu-id="39277-137">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="39277-138">Вы можете разбивать локализованные строки по контроллеру, области или использовать всего один конвейер.</span><span class="sxs-lookup"><span data-stu-id="39277-138">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="39277-139">В образце приложения для общих ресурсов применяется класс-заглушка с именем `SharedResource`.</span><span class="sxs-lookup"><span data-stu-id="39277-139">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="39277-140">Некоторые разработчики используют класс `Startup` для хранения глобальных или общих строк.</span><span class="sxs-lookup"><span data-stu-id="39277-140">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="39277-141">В примере ниже используются средства локализации `InfoController` и `SharedResource`.</span><span class="sxs-lookup"><span data-stu-id="39277-141">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="39277-142">Локализация представления</span><span class="sxs-lookup"><span data-stu-id="39277-142">View localization</span></span>

<span data-ttu-id="39277-143">Служба `IViewLocalizer` предоставляет локализованные строки для [представления](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="39277-143">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="39277-144">Класс `ViewLocalizer` реализует этот интерфейс и находит расположение ресурсов по пути к файлу представления.</span><span class="sxs-lookup"><span data-stu-id="39277-144">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="39277-145">В следующем примере кода демонстрируется использование реализации `IViewLocalizer` по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="39277-145">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="39277-146">Реализация `IViewLocalizer` по умолчанию находит файл ресурсов по имени файла представления.</span><span class="sxs-lookup"><span data-stu-id="39277-146">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="39277-147">Возможности использовать глобальный общий файл ресурсов нет.</span><span class="sxs-lookup"><span data-stu-id="39277-147">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="39277-148">`ViewLocalizer` реализует средство локализации с помощью интерфейса `IHtmlLocalizer`, поэтому Razor не кодирует локализованную строку в HTML.</span><span class="sxs-lookup"><span data-stu-id="39277-148">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="39277-149">Вы можете параметризовать строки ресурсов, и `IViewLocalizer` будет кодировать в HTML параметры, но не строки ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-149">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="39277-150">Рассмотрим следующую разметку Razor:</span><span class="sxs-lookup"><span data-stu-id="39277-150">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="39277-151">Файл ресурсов на французском языке может содержать следующие ресурсы:</span><span class="sxs-lookup"><span data-stu-id="39277-151">A French resource file could contain the following:</span></span>

| <span data-ttu-id="39277-152">Ключ</span><span class="sxs-lookup"><span data-stu-id="39277-152">Key</span></span> | <span data-ttu-id="39277-153">Значение</span><span class="sxs-lookup"><span data-stu-id="39277-153">Value</span></span> |
| ----- | ---
<span data-ttu-id="39277-154">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-154">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-155">'Blazor'</span></span>
- <span data-ttu-id="39277-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-156">'Identity'</span></span>
- <span data-ttu-id="39277-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-158">'Razor'</span></span>
- <span data-ttu-id="39277-159">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-159">'SignalR' uid:</span></span> 

<span data-ttu-id="39277-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="39277-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="39277-161">Преобразованное для просмотра представление будет содержать разметку HTML из файла ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-161">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="39277-162">**Примечание.** Как правило, требуется локализовать только текст, но не код HTML.</span><span class="sxs-lookup"><span data-stu-id="39277-162">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="39277-163">Чтобы использовать в представлении общий файл ресурсов, внедрите `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="39277-163">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="39277-164">Локализация DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="39277-164">DataAnnotations localization</span></span>

<span data-ttu-id="39277-165">Сообщения об ошибках DataAnnotations локализуются с помощью `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="39277-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="39277-166">При использовании параметра `ResourcesPath = "Resources"` сообщения об ошибках в `RegisterViewModel` могут сохраняться по одному из следующих путей:</span><span class="sxs-lookup"><span data-stu-id="39277-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="39277-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="39277-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="39277-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="39277-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="39277-169">В ASP.NET Core MVC 1.1.0 и более поздних версиях атрибуты, не относящиеся в проверке, локализуются.</span><span class="sxs-lookup"><span data-stu-id="39277-169">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="39277-170">В ASP.NET Core MVC 1.0 **не** производится поиск локализованных строк для атрибутов, не относящихся к проверке.</span><span class="sxs-lookup"><span data-stu-id="39277-170">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="39277-171">Использование одной строки ресурса для нескольких классов</span><span class="sxs-lookup"><span data-stu-id="39277-171">Using one resource string for multiple classes</span></span>

<span data-ttu-id="39277-172">В следующем коде показано, как использовать одну строку ресурса для атрибутов проверки с несколькими классами:</span><span class="sxs-lookup"><span data-stu-id="39277-172">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="39277-173">В предыдущем коде `SharedResource` — это класс, соответствующий файлу RESX, в котором хранятся сообщения о проверке.</span><span class="sxs-lookup"><span data-stu-id="39277-173">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="39277-174">При таком подходе DataAnnotations будут использовать только `SharedResource`, а не ресурс для каждого класса.</span><span class="sxs-lookup"><span data-stu-id="39277-174">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="39277-175">Предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="39277-175">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="39277-176">SupportedCultures и SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="39277-176">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="39277-177">ASP.NET Core позволяет указывать два значения языка и региональных параметров: `SupportedCultures` и `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="39277-177">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="39277-178">Объект [CultureInfo](/dotnet/api/system.globalization.cultureinfo) для `SupportedCultures` определяет результаты функций, зависящих от языка и региональных параметров: форматирование дат, времени, чисел, денежных единиц и т. д.</span><span class="sxs-lookup"><span data-stu-id="39277-178">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="39277-179">`SupportedCultures` также определяет порядок сортировки текста, соглашения о регистре символов и способы сравнения строк.</span><span class="sxs-lookup"><span data-stu-id="39277-179">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="39277-180">Дополнительные сведения о получении сервером значения языка и региональных параметров см. в описании свойства [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="39277-180">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="39277-181">Значение `SupportedUICultures` определяет то, какие строки переводов (в файле *RESX*) ищет объект [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="39277-181">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="39277-182">`ResourceManager` ищет связанные с языком и региональными параметрами строки, которые определяются значением `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="39277-182">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="39277-183">Каждый поток в .NET имеет объекты `CurrentCulture` и `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="39277-183">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="39277-184">ASP.NET Core проверяет эти значения при обработке функций, зависящих от языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-184">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="39277-185">Например, если для текущего потока заданы язык и региональные параметры "en-US" (английский, США), метод `DateTime.Now.ToLongDateString()` выводит строку "Thursday, February 18, 2016", но если `CurrentCulture` имеет значение "ru-RU" (русский, Россия), выводится строка "четверг, 18 февраля 2016 г."</span><span class="sxs-lookup"><span data-stu-id="39277-185">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="39277-186">Файлы ресурсов</span><span class="sxs-lookup"><span data-stu-id="39277-186">Resource files</span></span>

<span data-ttu-id="39277-187">Файл ресурсов — это полезное средство для отделения локализуемых строк от кода.</span><span class="sxs-lookup"><span data-stu-id="39277-187">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="39277-188">Переведенные строки на языках, отличных от языка по умолчанию, содержатся в отдельных файлах ресурсов с расширением *RESX*.</span><span class="sxs-lookup"><span data-stu-id="39277-188">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="39277-189">Например, вам нужно создать файл ресурсов для испанского языка с именем *Welcome.es.resx*, который будет содержать переведенные строки.</span><span class="sxs-lookup"><span data-stu-id="39277-189">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="39277-190">"es" — это код испанского языка.</span><span class="sxs-lookup"><span data-stu-id="39277-190">"es" is the language code for Spanish.</span></span> <span data-ttu-id="39277-191">Чтобы создать этот файл ресурсов в Visual Studio, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="39277-191">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="39277-192">В **обозревателе решений** щелкните правой кнопкой мыши папку, которая будет содержать файл ресурсов, а затем выберите пункты **Добавить** > **Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="39277-192">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Вложенное контекстное меню: в обозревателе решений контекстное меню открывается для папки ресурсов.](localization/_static/newi.png)

2. <span data-ttu-id="39277-195">В поле **Поиск установленных шаблонов** введите слово "ресурс" и укажите имя файла.</span><span class="sxs-lookup"><span data-stu-id="39277-195">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Диалоговое окно ''Добавление нового элемента''](localization/_static/res.png)

3. <span data-ttu-id="39277-197">Введите значение ключа (строку на исходном языке) в столбце **Имя** и переведенную строку в столбце **Значение**.</span><span class="sxs-lookup"><span data-stu-id="39277-197">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Файл Welcome.es.resx (файлов ресурсов с приветствиями на испанском языке) со словом "Hello" в столбце "Имя" и словом "Hol"a ("Hello" на испанском) в столбце "Значение"](localization/_static/hola.png)

    <span data-ttu-id="39277-199">В Visual Studio отобразится файл *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-199">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Обозреватель решений с файлом ресурсов на испанском языке](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="39277-201">Именование файлов ресурсов</span><span class="sxs-lookup"><span data-stu-id="39277-201">Resource file naming</span></span>

<span data-ttu-id="39277-202">Имена ресурсов представляют собой полные имена типов соответствующего класса за исключением имени сборки.</span><span class="sxs-lookup"><span data-stu-id="39277-202">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="39277-203">Например, ресурс на французском языке в проекте, главная сборка которого имеет имя `LocalizationWebsite.Web.dll`, для класса `LocalizationWebsite.Web.Startup` будет иметь имя *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-203">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="39277-204">Ресурс для класса `LocalizationWebsite.Web.Controllers.HomeController` будет иметь имя *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-204">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="39277-205">Если пространство имен целевого класса не совпадает с именем сборки, необходимо использовать полное имя типа.</span><span class="sxs-lookup"><span data-stu-id="39277-205">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="39277-206">Например, в образце проекта ресурс для типа `ExtraNamespace.Tools` будет иметь имя *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-206">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="39277-207">В образце проекта метод `ConfigureServices` присваивает свойству `ResourcesPath` значение "Resources", поэтому относительный путь к файлу ресурсов на французском языке для контроллера домашней страницы в проекте будет иметь вид *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-207">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="39277-208">Кроме того, для упорядочения файлов ресурсов можно использовать папки.</span><span class="sxs-lookup"><span data-stu-id="39277-208">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="39277-209">Для контроллера домашней страницы путь будет иметь вид *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-209">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="39277-210">Если параметр `ResourcesPath` не используется, файл *RESX* будет находиться в базовом каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="39277-210">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="39277-211">Файл ресурса для `HomeController` будет иметь имя *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-211">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="39277-212">Выбор соглашения об именовании на основе точечной нотации или пути зависит от того, как следует упорядочивать файлы ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-212">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="39277-213">Имя ресурса</span><span class="sxs-lookup"><span data-stu-id="39277-213">Resource name</span></span> | <span data-ttu-id="39277-214">Точечная нотация или путь</span><span class="sxs-lookup"><span data-stu-id="39277-214">Dot or path naming</span></span> |
| ---
<span data-ttu-id="39277-215">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-215">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-216">'Blazor'</span></span>
- <span data-ttu-id="39277-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-217">'Identity'</span></span>
- <span data-ttu-id="39277-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-219">'Razor'</span></span>
- <span data-ttu-id="39277-220">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-221">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-221">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-222">'Blazor'</span></span>
- <span data-ttu-id="39277-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-223">'Identity'</span></span>
- <span data-ttu-id="39277-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-225">'Razor'</span></span>
- <span data-ttu-id="39277-226">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-227">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-227">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-228">'Blazor'</span></span>
- <span data-ttu-id="39277-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-229">'Identity'</span></span>
- <span data-ttu-id="39277-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-231">'Razor'</span></span>
- <span data-ttu-id="39277-232">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-233">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-233">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-234">'Blazor'</span></span>
- <span data-ttu-id="39277-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-235">'Identity'</span></span>
- <span data-ttu-id="39277-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-237">'Razor'</span></span>
- <span data-ttu-id="39277-238">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-238">'SignalR' uid:</span></span> 

<span data-ttu-id="39277-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-240">'Blazor'</span></span>
- <span data-ttu-id="39277-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-241">'Identity'</span></span>
- <span data-ttu-id="39277-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-243">'Razor'</span></span>
- <span data-ttu-id="39277-244">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-245">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-245">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-246">'Blazor'</span></span>
- <span data-ttu-id="39277-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-247">'Identity'</span></span>
- <span data-ttu-id="39277-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-249">'Razor'</span></span>
- <span data-ttu-id="39277-250">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-251">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-251">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-252">'Blazor'</span></span>
- <span data-ttu-id="39277-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-253">'Identity'</span></span>
- <span data-ttu-id="39277-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-255">'Razor'</span></span>
- <span data-ttu-id="39277-256">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-257">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-257">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-258">'Blazor'</span></span>
- <span data-ttu-id="39277-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-259">'Identity'</span></span>
- <span data-ttu-id="39277-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-261">'Razor'</span></span>
- <span data-ttu-id="39277-262">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-262">'SignalR' uid:</span></span> 

<span data-ttu-id="39277-263">------- | | Resources/Controllers.HomeController.fr.resx | Точка  | | Resources/Controllers/HomeController.fr.resx  | Путь | |    |     |</span><span class="sxs-lookup"><span data-stu-id="39277-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="39277-264">Файлы ресурсов, для которых используется директива `@inject IViewLocalizer` в представлениях Razor, следуют той же модели.</span><span class="sxs-lookup"><span data-stu-id="39277-264">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="39277-265">Файлу ресурсов для представления может присваиваться имя на основе либо точечной нотации, либо пути.</span><span class="sxs-lookup"><span data-stu-id="39277-265">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="39277-266">В файле ресурсов для представления Razor имитируется путь к связанному файлу представления.</span><span class="sxs-lookup"><span data-stu-id="39277-266">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="39277-267">Допустим, свойству `ResourcesPath` присвоено значение "Resources". В этом случае файл ресурсов на французском языке, связанный с представлением *Views/Home/About.cshtml*, может иметь одно из следующих имен:</span><span class="sxs-lookup"><span data-stu-id="39277-267">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="39277-268">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="39277-268">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="39277-269">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="39277-269">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="39277-270">Если параметр `ResourcesPath` не используется, файл *RESX* для представления будет находиться в той же папке, что и представление.</span><span class="sxs-lookup"><span data-stu-id="39277-270">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="39277-271">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="39277-271">RootNamespaceAttribute</span></span> 

<span data-ttu-id="39277-272">Атрибут [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) содержит корневое пространство имен сборки, если корневое пространство имен сборки отличается от имени сборки.</span><span class="sxs-lookup"><span data-stu-id="39277-272">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="39277-273">Это может произойти, если имя проекта — недопустимый идентификатор .NET.</span><span class="sxs-lookup"><span data-stu-id="39277-273">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="39277-274">Например, `my-project-name.csproj` будет использовать корневое пространство имен `my_project_name` и имя сборки `my-project-name`, что повлечет эту ошибку.</span><span class="sxs-lookup"><span data-stu-id="39277-274">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="39277-275">Если корневое пространство имен сборки отличается от имени сборки</span><span class="sxs-lookup"><span data-stu-id="39277-275">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="39277-276">Локализация не работает по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39277-276">Localization does not work by default.</span></span>
* <span data-ttu-id="39277-277">Локализация завершается сбоем из-за метода поиска ресурсов в сборке.</span><span class="sxs-lookup"><span data-stu-id="39277-277">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="39277-278">`RootNamespace` — это значение во время сборки, которое недоступно выполняющемуся процессу.</span><span class="sxs-lookup"><span data-stu-id="39277-278">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="39277-279">Если `RootNamespace` отличается от `AssemblyName`, включите следующее в файл *AssemblyInfo.cs* (со значениями параметров, замененными фактическими значениями).</span><span class="sxs-lookup"><span data-stu-id="39277-279">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="39277-280">Приведенный выше код обеспечивает успешное разрешение RESX-файлов.</span><span class="sxs-lookup"><span data-stu-id="39277-280">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="39277-281">Резервный язык и региональные параметры</span><span class="sxs-lookup"><span data-stu-id="39277-281">Culture fallback behavior</span></span>

<span data-ttu-id="39277-282">При поиске ресурса локализации использует резервный язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-282">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="39277-283">Если запрошенный язык и региональные параметры не найдены, используется родительский язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-283">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="39277-284">Кстати, родительский язык и региональные параметры представляют свойство [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent).</span><span class="sxs-lookup"><span data-stu-id="39277-284">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="39277-285">Обычно (но не всегда) это означает удаление национального символа из ISO.</span><span class="sxs-lookup"><span data-stu-id="39277-285">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="39277-286">Например, диалект испанского в Мексике — es-MX.</span><span class="sxs-lookup"><span data-stu-id="39277-286">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="39277-287">Родительский элемент "es" (испанский) не относится к той или иной стране.</span><span class="sxs-lookup"><span data-stu-id="39277-287">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="39277-288">Допустим, сайт получает запрос на ресурс "Приветствие" с использованием языка и региональных параметров fr-CA.</span><span class="sxs-lookup"><span data-stu-id="39277-288">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="39277-289">Система локализации ищет в следующих ресурсах по порядку и выбирает первое совпадение:</span><span class="sxs-lookup"><span data-stu-id="39277-289">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="39277-290">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="39277-290">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="39277-291">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="39277-291">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="39277-292">*Welcome.resx* (если `NeutralResourcesLanguage` — fr-CA)</span><span class="sxs-lookup"><span data-stu-id="39277-292">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="39277-293">Если вы удалите указатель языка и региональных параметров ".fr" и при этом задан французский язык и региональные параметры, будет считан файл ресурсов по умолчанию и строки локализуются.</span><span class="sxs-lookup"><span data-stu-id="39277-293">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="39277-294">Диспетчер ресурсов назначает ресурс по умолчанию или резервный ресурс на тот случай, когда соответствия запрошенному языку и региональным параметрам не найдено.</span><span class="sxs-lookup"><span data-stu-id="39277-294">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="39277-295">Если нужно, чтобы при отсутствии ресурса для запрошенного языка и региональных параметров просто возвращался ключ, не используйте файл ресурсов по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39277-295">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="39277-296">Создание файлов ресурсов с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39277-296">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="39277-297">Если вы создаете в Visual Studio файл ресурсов, в имени которого не указаны язык и региональные параметры (например, *Welcome.resx*), Visual Studio создаст класс C# со свойством для каждой строки.</span><span class="sxs-lookup"><span data-stu-id="39277-297">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="39277-298">В ASP.NET Core обычно требуется иное поведение.</span><span class="sxs-lookup"><span data-stu-id="39277-298">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="39277-299">Как правило, файл ресурсов *RESX* по умолчанию (файл *RESX* без указания языка и региональных параметров) не используется.</span><span class="sxs-lookup"><span data-stu-id="39277-299">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="39277-300">Мы рекомендуем создать файл *RESX* с указанием языка и региональных параметров (например, *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="39277-300">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="39277-301">При создании файла *RESX* с указанием языка и региональных параметров среда Visual Studio не создает файл класса.</span><span class="sxs-lookup"><span data-stu-id="39277-301">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="39277-302">Добавление других языков и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="39277-302">Add other cultures</span></span>

<span data-ttu-id="39277-303">Каждое сочетание языка и региональных параметров (кроме языка по умолчанию) требует уникального файла ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-303">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="39277-304">Создавая файлы ресурсов для разных языков, языковых стандартов и региональных параметров, вы включаете в их имена коды языков ISO (например, **en-us**, **ru-ru** и **fr-ca**).</span><span class="sxs-lookup"><span data-stu-id="39277-304">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="39277-305">Эти коды ISO помещаются между именем файла и расширением *RESX*, например *Welcome.es-MX.resx* (испанский, Мексика).</span><span class="sxs-lookup"><span data-stu-id="39277-305">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="39277-306">реализацию стратегии по выбору языка и региональных параметров для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="39277-306">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="39277-307">Настройка локализации</span><span class="sxs-lookup"><span data-stu-id="39277-307">Configure localization</span></span>

<span data-ttu-id="39277-308">Локализация настраивается в методе `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="39277-308">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="39277-309">Метод `AddLocalization` добавляет службы локализации в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="39277-309">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="39277-310">В приведенном выше коде также задается путь к ресурсам "Resources".</span><span class="sxs-lookup"><span data-stu-id="39277-310">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="39277-311">Метод `AddViewLocalization` добавляет поддержку файлов локализованных представлений.</span><span class="sxs-lookup"><span data-stu-id="39277-311">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="39277-312">В этом примере локализация образца представления основана на суффиксе файла представления,</span><span class="sxs-lookup"><span data-stu-id="39277-312">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="39277-313">например "fr" в файле *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="39277-313">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="39277-314">Метод `AddDataAnnotationsLocalization` добавляет поддержку локализованных сообщений проверки `DataAnnotations` посредством абстракций `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="39277-314">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="39277-315">Промежуточный слой локализации</span><span class="sxs-lookup"><span data-stu-id="39277-315">Localization middleware</span></span>

<span data-ttu-id="39277-316">Текущий язык и региональные параметры в запросе задаются в [промежуточном слое](xref:fundamentals/middleware/index) локализации.</span><span class="sxs-lookup"><span data-stu-id="39277-316">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="39277-317">Промежуточный слой локализации включается в методе `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="39277-317">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="39277-318">Промежуточный слой локализации должен настраиваться перед другими промежуточными слоями, которые могут проверять язык и региональные параметры запроса (например, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="39277-318">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="39277-319">`UseRequestLocalization` инициализирует объект `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="39277-319">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="39277-320">При каждом запросе проверяется список поставщиков `RequestCultureProvider` в объекте `RequestLocalizationOptions` и используется первый поставщик, который может успешно определить язык и региональные параметры запроса.</span><span class="sxs-lookup"><span data-stu-id="39277-320">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="39277-321">Поставщики по умолчанию берутся из класса `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="39277-321">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="39277-322">В списке по умолчанию поставщики следуют от наиболее конкретных до наиболее общих.</span><span class="sxs-lookup"><span data-stu-id="39277-322">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="39277-323">Далее в этой статье вы узнаете, как можно изменить этот порядок и даже добавить пользовательский поставщик языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-323">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="39277-324">Если ни один из поставщиков не может определить язык и региональные параметры запроса, используется `DefaultRequestCulture`.</span><span class="sxs-lookup"><span data-stu-id="39277-324">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="39277-325">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="39277-325">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="39277-326">Некоторые приложения используют строку запроса для указания [языка и региональных параметров самого приложения и пользовательского интерфейса](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="39277-326">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="39277-327">Если в приложении для этого применяется файл cookie или заголовок Accept-Language, добавление строки запроса к URL-адресу может быть полезным в целях отладки и тестирования кода.</span><span class="sxs-lookup"><span data-stu-id="39277-327">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="39277-328">По умолчанию поставщик `QueryStringRequestCultureProvider` регистрируется в качестве первого поставщика локализации в списке `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="39277-328">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="39277-329">Вы передаете параметры строки запроса `culture` и `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="39277-329">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="39277-330">В следующем примере в качестве языка и региональных параметров (язык и регион) задаются испанский язык и Мексика:</span><span class="sxs-lookup"><span data-stu-id="39277-330">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="39277-331">Если передан только один из двух параметров (`culture` или `ui-culture`), поставщик строки запроса задаст с его помощью оба значения.</span><span class="sxs-lookup"><span data-stu-id="39277-331">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="39277-332">Например, если передан только язык и региональные параметры, будут заданы оба значения `Culture` и `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="39277-332">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="39277-333">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="39277-333">CookieRequestCultureProvider</span></span>

<span data-ttu-id="39277-334">Рабочие приложения часто предоставляют механизм для указания языка и региональных параметров с помощью соответствующего файла cookie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="39277-334">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="39277-335">Чтобы создать файл cookie, используйте метод `MakeCookieValue`.</span><span class="sxs-lookup"><span data-stu-id="39277-335">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="39277-336">`CookieRequestCultureProvider` `DefaultCookieName` возвращает имя файла cookie по умолчанию, с помощью которого отслеживается предпочтительный язык и региональные параметры пользователя.</span><span class="sxs-lookup"><span data-stu-id="39277-336">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="39277-337">Имя файла cookie по умолчанию — `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="39277-337">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="39277-338">Файл cookie имеет формат `c=%LANGCODE%|uic=%LANGCODE%`, где `c` — это `Culture`, а `uic` — это `UICulture`, например:</span><span class="sxs-lookup"><span data-stu-id="39277-338">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="39277-339">Если указаны либо только язык и региональные параметры, либо только язык и региональные параметры пользовательского интерфейса, это значение будет использоваться для обоих параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-339">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="39277-340">Заголовок HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="39277-340">The Accept-Language HTTP header</span></span>

<span data-ttu-id="39277-341">[Заголовок Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) может задаваться в большинстве браузеров и изначально предназначался для указания языка пользователя.</span><span class="sxs-lookup"><span data-stu-id="39277-341">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="39277-342">Он показывает, какой язык был выбран в браузере или унаследован от базовой операционной системы.</span><span class="sxs-lookup"><span data-stu-id="39277-342">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="39277-343">Заголовок HTTP Accept-Language в запросе из браузера — это не самый надежный способ определения предпочтительного языка пользователя (см. статью [Настройка языковых предпочтений в браузере](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="39277-343">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="39277-344">В рабочем приложении должна быть возможность выбора языка и региональных параметров пользователем.</span><span class="sxs-lookup"><span data-stu-id="39277-344">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="39277-345">Задание заголовка HTTP Accept-Language в Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="39277-345">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="39277-346">Щелкните значок шестеренки и выберите пункт **Свойства браузера**.</span><span class="sxs-lookup"><span data-stu-id="39277-346">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="39277-347">Нажмите кнопку **Языки**.</span><span class="sxs-lookup"><span data-stu-id="39277-347">Tap **Languages**.</span></span>

    ![Свойства браузера](localization/_static/lang.png)

3. <span data-ttu-id="39277-349">Нажмите **Задать предпочитаемые языки**.</span><span class="sxs-lookup"><span data-stu-id="39277-349">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="39277-350">Нажмите **Добавить язык**.</span><span class="sxs-lookup"><span data-stu-id="39277-350">Tap **Add a language**.</span></span>

5. <span data-ttu-id="39277-351">Добавьте язык.</span><span class="sxs-lookup"><span data-stu-id="39277-351">Add the language.</span></span>

6. <span data-ttu-id="39277-352">Выберите язык, а затем нажмите кнопку **Вверх**.</span><span class="sxs-lookup"><span data-stu-id="39277-352">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="39277-353">Использование пользовательского поставщика</span><span class="sxs-lookup"><span data-stu-id="39277-353">Use a custom provider</span></span>

<span data-ttu-id="39277-354">Предположим, необходимо, чтобы ваши клиенты могли сохранять информацию о своем языке и региональных параметрах в ваших базах данных.</span><span class="sxs-lookup"><span data-stu-id="39277-354">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="39277-355">Можно написать поставщик, который будет искать эти значения для пользователя.</span><span class="sxs-lookup"><span data-stu-id="39277-355">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="39277-356">В следующем примере кода показано, как добавить пользовательский поставщик:</span><span class="sxs-lookup"><span data-stu-id="39277-356">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="39277-357">Для добавления или удаления поставщиков локализации используйте объект `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="39277-357">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="39277-358">Задание языка и региональных параметров программным образом</span><span class="sxs-lookup"><span data-stu-id="39277-358">Set the culture programmatically</span></span>

<span data-ttu-id="39277-359">В образце проекта **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть пользовательский интерфейс для задания значения `Culture`.</span><span class="sxs-lookup"><span data-stu-id="39277-359">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="39277-360">Файл *Views/Shared/_SelectLanguagePartial.cshtml* позволяет выбрать язык и региональные параметры из списка поддерживаемых:</span><span class="sxs-lookup"><span data-stu-id="39277-360">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="39277-361">Файл *Views/Shared/_SelectLanguagePartial.cshtml* добавляется в раздел `footer` файла макета, поэтому он доступен всем представлениям:</span><span class="sxs-lookup"><span data-stu-id="39277-361">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="39277-362">Метод `SetLanguage` задает файл cookie языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-362">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="39277-363">Подключить файл *_SelectLanguagePartial.cshtml* к образцу кода этого проекта нельзя.</span><span class="sxs-lookup"><span data-stu-id="39277-363">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="39277-364">В проекте **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть код для передачи объекта `RequestLocalizationOptions` в частичное представление Razor посредством контейнера [внедрения зависимостей](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="39277-364">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="39277-365">Данные маршрутов привязки модели и строки запросов</span><span class="sxs-lookup"><span data-stu-id="39277-365">Model binding route data and query strings</span></span>

<span data-ttu-id="39277-366">См. раздел [Поведение глобализации для данных маршрутов привязки модели и строк запросов](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="39277-366">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="39277-367">Термины, относящиеся к глобализации и локализации</span><span class="sxs-lookup"><span data-stu-id="39277-367">Globalization and localization terms</span></span>

<span data-ttu-id="39277-368">Процесс локализации приложения требует базового понимания распространенных кодировок, часто используемых при разработке современного программного обеспечения, и связанных с ними проблем.</span><span class="sxs-lookup"><span data-stu-id="39277-368">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="39277-369">Хотя на всех компьютерах текст сохраняется в виде цифр (кодов), в разных системах эти коды для одного и того же текста различаются.</span><span class="sxs-lookup"><span data-stu-id="39277-369">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="39277-370">Под процессом локализации понимается перевод пользовательского интерфейса приложения для определенного языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-370">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="39277-371">[Возможность локализации](/dotnet/standard/globalization-localization/localizability-review) — это определяемое на промежуточной стадии состояние готовности глобализованного приложения к локализации.</span><span class="sxs-lookup"><span data-stu-id="39277-371">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="39277-372">Формат [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) для названия языка и региональных параметров имеет вид `<languagecode2>-<country/regioncode2>`, где `<languagecode2>` — это код языка, а `<country/regioncode2>` — код субкультуры.</span><span class="sxs-lookup"><span data-stu-id="39277-372">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="39277-373">Примеры: `es-CL` для испанского языка (Чили), `en-US` для английского языка (США), `en-AU` для английского языка (Австралия).</span><span class="sxs-lookup"><span data-stu-id="39277-373">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="39277-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) — это комбинация двухбуквенного кода культуры ISO 639 в нижнем регистре (он связан с языком) и двухбуквенного кода субкультуры ISO 3166 в верхнем регистре (он связан со страной или регионом).</span><span class="sxs-lookup"><span data-stu-id="39277-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="39277-375">См. статью [Имя языка и региональных параметров](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="39277-375">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="39277-376">Вместо слова "интернационализация" (internationalization) часто используют аббревиатуру "I18N".</span><span class="sxs-lookup"><span data-stu-id="39277-376">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="39277-377">В аббревиатуру включаются первая и последняя буквы слова, а также число букв между ними, то есть 18 — это число букв между первой ("I") и последней ("N") буквами.</span><span class="sxs-lookup"><span data-stu-id="39277-377">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="39277-378">То же самое касается глобализации (Globalization — G11N) и локализации (Localization — L10N).</span><span class="sxs-lookup"><span data-stu-id="39277-378">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="39277-379">Термины:</span><span class="sxs-lookup"><span data-stu-id="39277-379">Terms:</span></span>

* <span data-ttu-id="39277-380">Глобализация (G11N): процесс подготовки приложения к поддержке различных языков и регионов.</span><span class="sxs-lookup"><span data-stu-id="39277-380">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="39277-381">Локализация (L10N): процесс настройки приложения для работы с конкретным языком и регионом.</span><span class="sxs-lookup"><span data-stu-id="39277-381">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="39277-382">Интернационализация (I18N): описывает глобализацию и локализацию.</span><span class="sxs-lookup"><span data-stu-id="39277-382">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="39277-383">Язык и региональные параметры: обозначает язык и (необязательно) регион.</span><span class="sxs-lookup"><span data-stu-id="39277-383">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="39277-384">Нейтральный язык и региональные параметры: язык и региональные параметры, для которых указан язык, но не регион.</span><span class="sxs-lookup"><span data-stu-id="39277-384">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="39277-385">(например, "en", "es").</span><span class="sxs-lookup"><span data-stu-id="39277-385">(for example "en", "es")</span></span>
* <span data-ttu-id="39277-386">Конкретный язык и региональные параметры: язык и региональные параметры, для которых указаны язык и регион.</span><span class="sxs-lookup"><span data-stu-id="39277-386">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="39277-387">(например, "en-US", "en-GB", "es-CL").</span><span class="sxs-lookup"><span data-stu-id="39277-387">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="39277-388">Родительский язык и региональные параметры: нейтральные язык и региональные параметры, содержащие конкретные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-388">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="39277-389">(например, "en" — это родительские язык и региональные параметры для "en-US" и "en-GB").</span><span class="sxs-lookup"><span data-stu-id="39277-389">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="39277-390">Языковой стандарт: тот же, что и язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-390">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="39277-391">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="39277-391">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="39277-392">[Проект Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb), используемый в этой статье.</span><span class="sxs-lookup"><span data-stu-id="39277-392">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="39277-393">Глобализация и локализация приложений .NET</span><span class="sxs-lookup"><span data-stu-id="39277-393">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="39277-394">Ресурсы в RESX-файлах</span><span class="sxs-lookup"><span data-stu-id="39277-394">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="39277-395">Набор средств многоязычных приложений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="39277-395">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="39277-396">Локализация и универсальные шаблоны</span><span class="sxs-lookup"><span data-stu-id="39277-396">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="39277-397">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Дэмиен Боуден](https://twitter.com/damien_bod) (Damien Bowden), [Барт Каликсто](https://twitter.com/bartmax) (Bart Calixto), [Надим Афана](https://afana.me/) (Nadeem Afana) и [Хишам Бин Атея](https://twitter.com/hishambinateya) (Hisham Bin Ateya)</span><span class="sxs-lookup"><span data-stu-id="39277-397">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="39277-398">Создание многоязычного веб-сайта позволит расширить аудиторию.</span><span class="sxs-lookup"><span data-stu-id="39277-398">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="39277-399">ASP.NET Core предоставляет службы и ПО промежуточного слоя для локализации на разные языки и для разных региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-399">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="39277-400">Интернационализация предполагает [глобализацию](/dotnet/api/system.globalization) и [локализацию](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="39277-400">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="39277-401">Глобализация — это процесс разработки приложений, которые поддерживают разные языки и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-401">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="39277-402">Глобализация добавляет поддержку ввода отображения и вывода определенного набора языковых сценариев, относящихся к конкретным регионам.</span><span class="sxs-lookup"><span data-stu-id="39277-402">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="39277-403">Локализация — это процесс адаптации глобализованного приложения, уже подготовленного к локализации, к определенному языку, языковому стандарту и региональным параметрам.</span><span class="sxs-lookup"><span data-stu-id="39277-403">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="39277-404">Дополнительные сведения см. в разделе **Термины, относящиеся к глобализации и локализации** ближе к концу этого документа.</span><span class="sxs-lookup"><span data-stu-id="39277-404">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="39277-405">Локализация приложения включает следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="39277-405">App localization involves the following:</span></span>

1. <span data-ttu-id="39277-406">обеспечение возможности локализации для содержимого приложения;</span><span class="sxs-lookup"><span data-stu-id="39277-406">Make the app's content localizable</span></span>
1. <span data-ttu-id="39277-407">предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров;</span><span class="sxs-lookup"><span data-stu-id="39277-407">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="39277-408">реализацию стратегии по выбору языка и региональных параметров для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="39277-408">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="39277-409">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="39277-409">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="39277-410">Обеспечение возможности локализации для содержимого приложения</span><span class="sxs-lookup"><span data-stu-id="39277-410">Make the app's content localizable</span></span>

<span data-ttu-id="39277-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` не требует сохранять строки на языке по умолчанию в файле ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="39277-412">Вы можете разрабатывать приложение, предназначенное для локализации, не создавая файлы ресурсов на ранних этапах разработки.</span><span class="sxs-lookup"><span data-stu-id="39277-412">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="39277-413">В приведенном ниже коде показано, как подготовить строку "About Title" для локализации.</span><span class="sxs-lookup"><span data-stu-id="39277-413">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="39277-414">В предыдущем коде реализация `IStringLocalizer<T>` получена в результате [внедрения зависимостей](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="39277-414">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="39277-415">Если локализованное значение для строки "About Title" не найдено, возвращается ключ индексатора, то есть строка "About Title".</span><span class="sxs-lookup"><span data-stu-id="39277-415">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="39277-416">Вы можете оставить литеральные строки на языке по умолчанию и заключить их в средство локализации, чтобы сосредоточиться на разработке приложения.</span><span class="sxs-lookup"><span data-stu-id="39277-416">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="39277-417">Вы разрабатываете приложение на языке по умолчанию и подготавливаете его к локализации, не создавая предварительно файл ресурсов по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39277-417">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="39277-418">Вы также можете выбрать традиционный подход и предоставить ключ для извлечения строки на языке по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39277-418">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="39277-419">Для многих разработчиков новый рабочий процесс, который не требует наличия файла *RESX* на языке по умолчанию и позволяет просто заключать строки для дальнейшей обработки, может помочь снизить затраты на локализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="39277-419">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="39277-420">Другие разработчики могут предпочесть традиционный рабочий процесс, так как он упрощает работу с более длинными строками и обновление локализованных строк.</span><span class="sxs-lookup"><span data-stu-id="39277-420">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="39277-421">Используйте реализацию `IHtmlLocalizer<T>` для ресурсов, содержащих код HTML.</span><span class="sxs-lookup"><span data-stu-id="39277-421">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="39277-422">Интерфейс `IHtmlLocalizer` кодирует в HTML аргументы, отформатированные в строке ресурса, но не кодирует в HTML саму строку.</span><span class="sxs-lookup"><span data-stu-id="39277-422">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="39277-423">В выделенной ниже строке в HTML кодируется только значение параметра `name`.</span><span class="sxs-lookup"><span data-stu-id="39277-423">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="39277-424">**Примечание.** Как правило, требуется локализовать только текст, но не код HTML.</span><span class="sxs-lookup"><span data-stu-id="39277-424">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="39277-425">На самом нижнем уровне интерфейс `IStringLocalizerFactory` можно получить из [внедрения зависимостей](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="39277-425">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="39277-426">В приведенном выше коде демонстрируются оба фабричных метода создания.</span><span class="sxs-lookup"><span data-stu-id="39277-426">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="39277-427">Вы можете разбивать локализованные строки по контроллеру, области или использовать всего один конвейер.</span><span class="sxs-lookup"><span data-stu-id="39277-427">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="39277-428">В образце приложения для общих ресурсов применяется класс-заглушка с именем `SharedResource`.</span><span class="sxs-lookup"><span data-stu-id="39277-428">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="39277-429">Некоторые разработчики используют класс `Startup` для хранения глобальных или общих строк.</span><span class="sxs-lookup"><span data-stu-id="39277-429">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="39277-430">В примере ниже используются средства локализации `InfoController` и `SharedResource`.</span><span class="sxs-lookup"><span data-stu-id="39277-430">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="39277-431">Локализация представления</span><span class="sxs-lookup"><span data-stu-id="39277-431">View localization</span></span>

<span data-ttu-id="39277-432">Служба `IViewLocalizer` предоставляет локализованные строки для [представления](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="39277-432">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="39277-433">Класс `ViewLocalizer` реализует этот интерфейс и находит расположение ресурсов по пути к файлу представления.</span><span class="sxs-lookup"><span data-stu-id="39277-433">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="39277-434">В следующем примере кода демонстрируется использование реализации `IViewLocalizer` по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="39277-434">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="39277-435">Реализация `IViewLocalizer` по умолчанию находит файл ресурсов по имени файла представления.</span><span class="sxs-lookup"><span data-stu-id="39277-435">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="39277-436">Возможности использовать глобальный общий файл ресурсов нет.</span><span class="sxs-lookup"><span data-stu-id="39277-436">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="39277-437">`ViewLocalizer` реализует средство локализации с помощью интерфейса `IHtmlLocalizer`, поэтому Razor не кодирует локализованную строку в HTML.</span><span class="sxs-lookup"><span data-stu-id="39277-437">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="39277-438">Вы можете параметризовать строки ресурсов, и `IViewLocalizer` будет кодировать в HTML параметры, но не строки ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-438">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="39277-439">Рассмотрим следующую разметку Razor:</span><span class="sxs-lookup"><span data-stu-id="39277-439">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="39277-440">Файл ресурсов на французском языке может содержать следующие ресурсы:</span><span class="sxs-lookup"><span data-stu-id="39277-440">A French resource file could contain the following:</span></span>

| <span data-ttu-id="39277-441">Ключ</span><span class="sxs-lookup"><span data-stu-id="39277-441">Key</span></span> | <span data-ttu-id="39277-442">Значение</span><span class="sxs-lookup"><span data-stu-id="39277-442">Value</span></span> |
| ----- | ---
<span data-ttu-id="39277-443">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-443">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-444">'Blazor'</span></span>
- <span data-ttu-id="39277-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-445">'Identity'</span></span>
- <span data-ttu-id="39277-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-447">'Razor'</span></span>
- <span data-ttu-id="39277-448">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-448">'SignalR' uid:</span></span> 

<span data-ttu-id="39277-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="39277-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="39277-450">Преобразованное для просмотра представление будет содержать разметку HTML из файла ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-450">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="39277-451">**Примечание.** Как правило, требуется локализовать только текст, но не код HTML.</span><span class="sxs-lookup"><span data-stu-id="39277-451">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="39277-452">Чтобы использовать в представлении общий файл ресурсов, внедрите `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="39277-452">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="39277-453">Локализация DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="39277-453">DataAnnotations localization</span></span>

<span data-ttu-id="39277-454">Сообщения об ошибках DataAnnotations локализуются с помощью `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="39277-454">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="39277-455">При использовании параметра `ResourcesPath = "Resources"` сообщения об ошибках в `RegisterViewModel` могут сохраняться по одному из следующих путей:</span><span class="sxs-lookup"><span data-stu-id="39277-455">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="39277-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="39277-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="39277-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="39277-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="39277-458">В ASP.NET Core MVC 1.1.0 и более поздних версиях атрибуты, не относящиеся в проверке, локализуются.</span><span class="sxs-lookup"><span data-stu-id="39277-458">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="39277-459">В ASP.NET Core MVC 1.0 **не** производится поиск локализованных строк для атрибутов, не относящихся к проверке.</span><span class="sxs-lookup"><span data-stu-id="39277-459">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="39277-460">Использование одной строки ресурса для нескольких классов</span><span class="sxs-lookup"><span data-stu-id="39277-460">Using one resource string for multiple classes</span></span>

<span data-ttu-id="39277-461">В следующем коде показано, как использовать одну строку ресурса для атрибутов проверки с несколькими классами:</span><span class="sxs-lookup"><span data-stu-id="39277-461">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="39277-462">В предыдущем коде `SharedResource` — это класс, соответствующий файлу RESX, в котором хранятся сообщения о проверке.</span><span class="sxs-lookup"><span data-stu-id="39277-462">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="39277-463">При таком подходе DataAnnotations будут использовать только `SharedResource`, а не ресурс для каждого класса.</span><span class="sxs-lookup"><span data-stu-id="39277-463">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="39277-464">Предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="39277-464">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="39277-465">SupportedCultures и SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="39277-465">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="39277-466">ASP.NET Core позволяет указывать два значения языка и региональных параметров: `SupportedCultures` и `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="39277-466">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="39277-467">Объект [CultureInfo](/dotnet/api/system.globalization.cultureinfo) для `SupportedCultures` определяет результаты функций, зависящих от языка и региональных параметров: форматирование дат, времени, чисел, денежных единиц и т. д.</span><span class="sxs-lookup"><span data-stu-id="39277-467">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="39277-468">`SupportedCultures` также определяет порядок сортировки текста, соглашения о регистре символов и способы сравнения строк.</span><span class="sxs-lookup"><span data-stu-id="39277-468">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="39277-469">Дополнительные сведения о получении сервером значения языка и региональных параметров см. в описании свойства [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="39277-469">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="39277-470">Значение `SupportedUICultures` определяет то, какие строки переводов (в файле *RESX*) ищет объект [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="39277-470">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="39277-471">`ResourceManager` ищет связанные с языком и региональными параметрами строки, которые определяются значением `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="39277-471">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="39277-472">Каждый поток в .NET имеет объекты `CurrentCulture` и `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="39277-472">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="39277-473">ASP.NET Core проверяет эти значения при обработке функций, зависящих от языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-473">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="39277-474">Например, если для текущего потока заданы язык и региональные параметры "en-US" (английский, США), метод `DateTime.Now.ToLongDateString()` выводит строку "Thursday, February 18, 2016", но если `CurrentCulture` имеет значение "ru-RU" (русский, Россия), выводится строка "четверг, 18 февраля 2016 г."</span><span class="sxs-lookup"><span data-stu-id="39277-474">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="39277-475">Файлы ресурсов</span><span class="sxs-lookup"><span data-stu-id="39277-475">Resource files</span></span>

<span data-ttu-id="39277-476">Файл ресурсов — это полезное средство для отделения локализуемых строк от кода.</span><span class="sxs-lookup"><span data-stu-id="39277-476">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="39277-477">Переведенные строки на языках, отличных от языка по умолчанию, содержатся в отдельных файлах ресурсов с расширением *RESX*.</span><span class="sxs-lookup"><span data-stu-id="39277-477">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="39277-478">Например, вам нужно создать файл ресурсов для испанского языка с именем *Welcome.es.resx*, который будет содержать переведенные строки.</span><span class="sxs-lookup"><span data-stu-id="39277-478">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="39277-479">"es" — это код испанского языка.</span><span class="sxs-lookup"><span data-stu-id="39277-479">"es" is the language code for Spanish.</span></span> <span data-ttu-id="39277-480">Чтобы создать этот файл ресурсов в Visual Studio, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="39277-480">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="39277-481">В **обозревателе решений** щелкните правой кнопкой мыши папку, которая будет содержать файл ресурсов, а затем выберите пункты **Добавить** > **Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="39277-481">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Вложенное контекстное меню: в обозревателе решений контекстное меню открывается для папки ресурсов.](localization/_static/newi.png)

2. <span data-ttu-id="39277-484">В поле **Поиск установленных шаблонов** введите слово "ресурс" и укажите имя файла.</span><span class="sxs-lookup"><span data-stu-id="39277-484">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Диалоговое окно ''Добавление нового элемента''](localization/_static/res.png)

3. <span data-ttu-id="39277-486">Введите значение ключа (строку на исходном языке) в столбце **Имя** и переведенную строку в столбце **Значение**.</span><span class="sxs-lookup"><span data-stu-id="39277-486">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Файл Welcome.es.resx (файлов ресурсов с приветствиями на испанском языке) со словом "Hello" в столбце "Имя" и словом "Hol"a ("Hello" на испанском) в столбце "Значение"](localization/_static/hola.png)

    <span data-ttu-id="39277-488">В Visual Studio отобразится файл *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-488">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Обозреватель решений с файлом ресурсов на испанском языке](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="39277-490">Именование файлов ресурсов</span><span class="sxs-lookup"><span data-stu-id="39277-490">Resource file naming</span></span>

<span data-ttu-id="39277-491">Имена ресурсов представляют собой полные имена типов соответствующего класса за исключением имени сборки.</span><span class="sxs-lookup"><span data-stu-id="39277-491">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="39277-492">Например, ресурс на французском языке в проекте, главная сборка которого имеет имя `LocalizationWebsite.Web.dll`, для класса `LocalizationWebsite.Web.Startup` будет иметь имя *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-492">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="39277-493">Ресурс для класса `LocalizationWebsite.Web.Controllers.HomeController` будет иметь имя *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-493">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="39277-494">Если пространство имен целевого класса не совпадает с именем сборки, необходимо использовать полное имя типа.</span><span class="sxs-lookup"><span data-stu-id="39277-494">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="39277-495">Например, в образце проекта ресурс для типа `ExtraNamespace.Tools` будет иметь имя *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-495">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="39277-496">В образце проекта метод `ConfigureServices` присваивает свойству `ResourcesPath` значение "Resources", поэтому относительный путь к файлу ресурсов на французском языке для контроллера домашней страницы в проекте будет иметь вид *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-496">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="39277-497">Кроме того, для упорядочения файлов ресурсов можно использовать папки.</span><span class="sxs-lookup"><span data-stu-id="39277-497">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="39277-498">Для контроллера домашней страницы путь будет иметь вид *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-498">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="39277-499">Если параметр `ResourcesPath` не используется, файл *RESX* будет находиться в базовом каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="39277-499">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="39277-500">Файл ресурса для `HomeController` будет иметь имя *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-500">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="39277-501">Выбор соглашения об именовании на основе точечной нотации или пути зависит от того, как следует упорядочивать файлы ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-501">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="39277-502">Имя ресурса</span><span class="sxs-lookup"><span data-stu-id="39277-502">Resource name</span></span> | <span data-ttu-id="39277-503">Точечная нотация или путь</span><span class="sxs-lookup"><span data-stu-id="39277-503">Dot or path naming</span></span> |
| ---
<span data-ttu-id="39277-504">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-504">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-505">'Blazor'</span></span>
- <span data-ttu-id="39277-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-506">'Identity'</span></span>
- <span data-ttu-id="39277-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-508">'Razor'</span></span>
- <span data-ttu-id="39277-509">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-510">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-510">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-511">'Blazor'</span></span>
- <span data-ttu-id="39277-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-512">'Identity'</span></span>
- <span data-ttu-id="39277-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-514">'Razor'</span></span>
- <span data-ttu-id="39277-515">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-516">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-516">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-517">'Blazor'</span></span>
- <span data-ttu-id="39277-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-518">'Identity'</span></span>
- <span data-ttu-id="39277-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-520">'Razor'</span></span>
- <span data-ttu-id="39277-521">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-522">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-522">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-523">'Blazor'</span></span>
- <span data-ttu-id="39277-524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-524">'Identity'</span></span>
- <span data-ttu-id="39277-525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-525">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-526">'Razor'</span></span>
- <span data-ttu-id="39277-527">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-527">'SignalR' uid:</span></span> 

<span data-ttu-id="39277-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-529">'Blazor'</span></span>
- <span data-ttu-id="39277-530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-530">'Identity'</span></span>
- <span data-ttu-id="39277-531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-531">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-532">'Razor'</span></span>
- <span data-ttu-id="39277-533">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-533">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-534">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-534">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-535">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-535">'Blazor'</span></span>
- <span data-ttu-id="39277-536">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-536">'Identity'</span></span>
- <span data-ttu-id="39277-537">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-537">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-538">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-538">'Razor'</span></span>
- <span data-ttu-id="39277-539">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-539">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-540">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-540">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-541">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-541">'Blazor'</span></span>
- <span data-ttu-id="39277-542">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-542">'Identity'</span></span>
- <span data-ttu-id="39277-543">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-543">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-544">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-544">'Razor'</span></span>
- <span data-ttu-id="39277-545">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-545">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-546">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-546">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-547">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-547">'Blazor'</span></span>
- <span data-ttu-id="39277-548">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-548">'Identity'</span></span>
- <span data-ttu-id="39277-549">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-549">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-550">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-550">'Razor'</span></span>
- <span data-ttu-id="39277-551">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-551">'SignalR' uid:</span></span> 

<span data-ttu-id="39277-552">------- | | Resources/Controllers.HomeController.fr.resx | Точка  | | Resources/Controllers/HomeController.fr.resx  | Путь | |    |     |</span><span class="sxs-lookup"><span data-stu-id="39277-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="39277-553">Файлы ресурсов, для которых используется директива `@inject IViewLocalizer` в представлениях Razor, следуют той же модели.</span><span class="sxs-lookup"><span data-stu-id="39277-553">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="39277-554">Файлу ресурсов для представления может присваиваться имя на основе либо точечной нотации, либо пути.</span><span class="sxs-lookup"><span data-stu-id="39277-554">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="39277-555">В файле ресурсов для представления Razor имитируется путь к связанному файлу представления.</span><span class="sxs-lookup"><span data-stu-id="39277-555">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="39277-556">Допустим, свойству `ResourcesPath` присвоено значение "Resources". В этом случае файл ресурсов на французском языке, связанный с представлением *Views/Home/About.cshtml*, может иметь одно из следующих имен:</span><span class="sxs-lookup"><span data-stu-id="39277-556">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="39277-557">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="39277-557">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="39277-558">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="39277-558">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="39277-559">Если параметр `ResourcesPath` не используется, файл *RESX* для представления будет находиться в той же папке, что и представление.</span><span class="sxs-lookup"><span data-stu-id="39277-559">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="39277-560">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="39277-560">RootNamespaceAttribute</span></span> 

<span data-ttu-id="39277-561">Атрибут [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) содержит корневое пространство имен сборки, если корневое пространство имен сборки отличается от имени сборки.</span><span class="sxs-lookup"><span data-stu-id="39277-561">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="39277-562">Это может произойти, если имя проекта — недопустимый идентификатор .NET.</span><span class="sxs-lookup"><span data-stu-id="39277-562">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="39277-563">Например, `my-project-name.csproj` будет использовать корневое пространство имен `my_project_name` и имя сборки `my-project-name`, что повлечет эту ошибку.</span><span class="sxs-lookup"><span data-stu-id="39277-563">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="39277-564">Если корневое пространство имен сборки отличается от имени сборки</span><span class="sxs-lookup"><span data-stu-id="39277-564">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="39277-565">Локализация не работает по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39277-565">Localization does not work by default.</span></span>
* <span data-ttu-id="39277-566">Локализация завершается сбоем из-за метода поиска ресурсов в сборке.</span><span class="sxs-lookup"><span data-stu-id="39277-566">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="39277-567">`RootNamespace` — это значение во время сборки, которое недоступно выполняющемуся процессу.</span><span class="sxs-lookup"><span data-stu-id="39277-567">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="39277-568">Если `RootNamespace` отличается от `AssemblyName`, включите следующее в файл *AssemblyInfo.cs* (со значениями параметров, замененными фактическими значениями).</span><span class="sxs-lookup"><span data-stu-id="39277-568">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="39277-569">Приведенный выше код обеспечивает успешное разрешение RESX-файлов.</span><span class="sxs-lookup"><span data-stu-id="39277-569">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="39277-570">Резервный язык и региональные параметры</span><span class="sxs-lookup"><span data-stu-id="39277-570">Culture fallback behavior</span></span>

<span data-ttu-id="39277-571">При поиске ресурса локализации использует резервный язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-571">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="39277-572">Если запрошенный язык и региональные параметры не найдены, используется родительский язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-572">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="39277-573">Кстати, родительский язык и региональные параметры представляют свойство [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent).</span><span class="sxs-lookup"><span data-stu-id="39277-573">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="39277-574">Обычно (но не всегда) это означает удаление национального символа из ISO.</span><span class="sxs-lookup"><span data-stu-id="39277-574">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="39277-575">Например, диалект испанского в Мексике — es-MX.</span><span class="sxs-lookup"><span data-stu-id="39277-575">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="39277-576">Родительский элемент "es" (испанский) не относится к той или иной стране.</span><span class="sxs-lookup"><span data-stu-id="39277-576">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="39277-577">Допустим, сайт получает запрос на ресурс "Приветствие" с использованием языка и региональных параметров fr-CA.</span><span class="sxs-lookup"><span data-stu-id="39277-577">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="39277-578">Система локализации ищет в следующих ресурсах по порядку и выбирает первое совпадение:</span><span class="sxs-lookup"><span data-stu-id="39277-578">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="39277-579">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="39277-579">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="39277-580">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="39277-580">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="39277-581">*Welcome.resx* (если `NeutralResourcesLanguage` — fr-CA)</span><span class="sxs-lookup"><span data-stu-id="39277-581">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="39277-582">Если вы удалите указатель языка и региональных параметров ".fr" и при этом задан французский язык и региональные параметры, будет считан файл ресурсов по умолчанию и строки локализуются.</span><span class="sxs-lookup"><span data-stu-id="39277-582">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="39277-583">Диспетчер ресурсов назначает ресурс по умолчанию или резервный ресурс на тот случай, когда соответствия запрошенному языку и региональным параметрам не найдено.</span><span class="sxs-lookup"><span data-stu-id="39277-583">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="39277-584">Если нужно, чтобы при отсутствии ресурса для запрошенного языка и региональных параметров просто возвращался ключ, не используйте файл ресурсов по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39277-584">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="39277-585">Создание файлов ресурсов с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39277-585">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="39277-586">Если вы создаете в Visual Studio файл ресурсов, в имени которого не указаны язык и региональные параметры (например, *Welcome.resx*), Visual Studio создаст класс C# со свойством для каждой строки.</span><span class="sxs-lookup"><span data-stu-id="39277-586">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="39277-587">В ASP.NET Core обычно требуется иное поведение.</span><span class="sxs-lookup"><span data-stu-id="39277-587">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="39277-588">Как правило, файл ресурсов *RESX* по умолчанию (файл *RESX* без указания языка и региональных параметров) не используется.</span><span class="sxs-lookup"><span data-stu-id="39277-588">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="39277-589">Мы рекомендуем создать файл *RESX* с указанием языка и региональных параметров (например, *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="39277-589">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="39277-590">При создании файла *RESX* с указанием языка и региональных параметров среда Visual Studio не создает файл класса.</span><span class="sxs-lookup"><span data-stu-id="39277-590">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="39277-591">Добавление других языков и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="39277-591">Add other cultures</span></span>

<span data-ttu-id="39277-592">Каждое сочетание языка и региональных параметров (кроме языка по умолчанию) требует уникального файла ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-592">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="39277-593">Создавая файлы ресурсов для разных языков, языковых стандартов и региональных параметров, вы включаете в их имена коды языков ISO (например, **en-us**, **ru-ru** и **fr-ca**).</span><span class="sxs-lookup"><span data-stu-id="39277-593">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="39277-594">Эти коды ISO помещаются между именем файла и расширением *RESX*, например *Welcome.es-MX.resx* (испанский, Мексика).</span><span class="sxs-lookup"><span data-stu-id="39277-594">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="39277-595">реализацию стратегии по выбору языка и региональных параметров для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="39277-595">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="39277-596">Настройка локализации</span><span class="sxs-lookup"><span data-stu-id="39277-596">Configure localization</span></span>

<span data-ttu-id="39277-597">Локализация настраивается в методе `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="39277-597">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="39277-598">Метод `AddLocalization` добавляет службы локализации в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="39277-598">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="39277-599">В приведенном выше коде также задается путь к ресурсам "Resources".</span><span class="sxs-lookup"><span data-stu-id="39277-599">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="39277-600">Метод `AddViewLocalization` добавляет поддержку файлов локализованных представлений.</span><span class="sxs-lookup"><span data-stu-id="39277-600">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="39277-601">В этом примере локализация образца представления основана на суффиксе файла представления,</span><span class="sxs-lookup"><span data-stu-id="39277-601">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="39277-602">например "fr" в файле *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="39277-602">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="39277-603">Метод `AddDataAnnotationsLocalization` добавляет поддержку локализованных сообщений проверки `DataAnnotations` посредством абстракций `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="39277-603">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="39277-604">Промежуточный слой локализации</span><span class="sxs-lookup"><span data-stu-id="39277-604">Localization middleware</span></span>

<span data-ttu-id="39277-605">Текущий язык и региональные параметры в запросе задаются в [промежуточном слое](xref:fundamentals/middleware/index) локализации.</span><span class="sxs-lookup"><span data-stu-id="39277-605">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="39277-606">Промежуточный слой локализации включается в методе `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="39277-606">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="39277-607">Промежуточный слой локализации должен настраиваться перед другими промежуточными слоями, которые могут проверять язык и региональные параметры запроса (например, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="39277-607">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="39277-608">`UseRequestLocalization` инициализирует объект `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="39277-608">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="39277-609">При каждом запросе проверяется список поставщиков `RequestCultureProvider` в объекте `RequestLocalizationOptions` и используется первый поставщик, который может успешно определить язык и региональные параметры запроса.</span><span class="sxs-lookup"><span data-stu-id="39277-609">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="39277-610">Поставщики по умолчанию берутся из класса `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="39277-610">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="39277-611">В списке по умолчанию поставщики следуют от наиболее конкретных до наиболее общих.</span><span class="sxs-lookup"><span data-stu-id="39277-611">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="39277-612">Далее в этой статье вы узнаете, как можно изменить этот порядок и даже добавить пользовательский поставщик языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-612">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="39277-613">Если ни один из поставщиков не может определить язык и региональные параметры запроса, используется `DefaultRequestCulture`.</span><span class="sxs-lookup"><span data-stu-id="39277-613">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="39277-614">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="39277-614">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="39277-615">Некоторые приложения используют строку запроса для указания [языка и региональных параметров самого приложения и пользовательского интерфейса](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="39277-615">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="39277-616">Если в приложении для этого применяется файл cookie или заголовок Accept-Language, добавление строки запроса к URL-адресу может быть полезным в целях отладки и тестирования кода.</span><span class="sxs-lookup"><span data-stu-id="39277-616">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="39277-617">По умолчанию поставщик `QueryStringRequestCultureProvider` регистрируется в качестве первого поставщика локализации в списке `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="39277-617">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="39277-618">Вы передаете параметры строки запроса `culture` и `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="39277-618">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="39277-619">В следующем примере в качестве языка и региональных параметров (язык и регион) задаются испанский язык и Мексика:</span><span class="sxs-lookup"><span data-stu-id="39277-619">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="39277-620">Если передан только один из двух параметров (`culture` или `ui-culture`), поставщик строки запроса задаст с его помощью оба значения.</span><span class="sxs-lookup"><span data-stu-id="39277-620">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="39277-621">Например, если передан только язык и региональные параметры, будут заданы оба значения `Culture` и `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="39277-621">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="39277-622">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="39277-622">CookieRequestCultureProvider</span></span>

<span data-ttu-id="39277-623">Рабочие приложения часто предоставляют механизм для указания языка и региональных параметров с помощью соответствующего файла cookie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="39277-623">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="39277-624">Чтобы создать файл cookie, используйте метод `MakeCookieValue`.</span><span class="sxs-lookup"><span data-stu-id="39277-624">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="39277-625">`CookieRequestCultureProvider` `DefaultCookieName` возвращает имя файла cookie по умолчанию, с помощью которого отслеживается предпочтительный язык и региональные параметры пользователя.</span><span class="sxs-lookup"><span data-stu-id="39277-625">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="39277-626">Имя файла cookie по умолчанию — `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="39277-626">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="39277-627">Файл cookie имеет формат `c=%LANGCODE%|uic=%LANGCODE%`, где `c` — это `Culture`, а `uic` — это `UICulture`, например:</span><span class="sxs-lookup"><span data-stu-id="39277-627">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="39277-628">Если указаны либо только язык и региональные параметры, либо только язык и региональные параметры пользовательского интерфейса, это значение будет использоваться для обоих параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-628">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="39277-629">Заголовок HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="39277-629">The Accept-Language HTTP header</span></span>

<span data-ttu-id="39277-630">[Заголовок Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) может задаваться в большинстве браузеров и изначально предназначался для указания языка пользователя.</span><span class="sxs-lookup"><span data-stu-id="39277-630">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="39277-631">Он показывает, какой язык был выбран в браузере или унаследован от базовой операционной системы.</span><span class="sxs-lookup"><span data-stu-id="39277-631">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="39277-632">Заголовок HTTP Accept-Language в запросе из браузера — это не самый надежный способ определения предпочтительного языка пользователя (см. статью [Настройка языковых предпочтений в браузере](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="39277-632">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="39277-633">В рабочем приложении должна быть возможность выбора языка и региональных параметров пользователем.</span><span class="sxs-lookup"><span data-stu-id="39277-633">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="39277-634">Задание заголовка HTTP Accept-Language в Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="39277-634">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="39277-635">Щелкните значок шестеренки и выберите пункт **Свойства браузера**.</span><span class="sxs-lookup"><span data-stu-id="39277-635">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="39277-636">Нажмите кнопку **Языки**.</span><span class="sxs-lookup"><span data-stu-id="39277-636">Tap **Languages**.</span></span>

    ![Свойства браузера](localization/_static/lang.png)

3. <span data-ttu-id="39277-638">Нажмите **Задать предпочитаемые языки**.</span><span class="sxs-lookup"><span data-stu-id="39277-638">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="39277-639">Нажмите **Добавить язык**.</span><span class="sxs-lookup"><span data-stu-id="39277-639">Tap **Add a language**.</span></span>

5. <span data-ttu-id="39277-640">Добавьте язык.</span><span class="sxs-lookup"><span data-stu-id="39277-640">Add the language.</span></span>

6. <span data-ttu-id="39277-641">Выберите язык, а затем нажмите кнопку **Вверх**.</span><span class="sxs-lookup"><span data-stu-id="39277-641">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="39277-642">Использование пользовательского поставщика</span><span class="sxs-lookup"><span data-stu-id="39277-642">Use a custom provider</span></span>

<span data-ttu-id="39277-643">Предположим, необходимо, чтобы ваши клиенты могли сохранять информацию о своем языке и региональных параметрах в ваших базах данных.</span><span class="sxs-lookup"><span data-stu-id="39277-643">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="39277-644">Можно написать поставщик, который будет искать эти значения для пользователя.</span><span class="sxs-lookup"><span data-stu-id="39277-644">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="39277-645">В следующем примере кода показано, как добавить пользовательский поставщик:</span><span class="sxs-lookup"><span data-stu-id="39277-645">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="39277-646">Для добавления или удаления поставщиков локализации используйте объект `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="39277-646">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="39277-647">Задание языка и региональных параметров программным образом</span><span class="sxs-lookup"><span data-stu-id="39277-647">Set the culture programmatically</span></span>

<span data-ttu-id="39277-648">В образце проекта **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть пользовательский интерфейс для задания значения `Culture`.</span><span class="sxs-lookup"><span data-stu-id="39277-648">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="39277-649">Файл *Views/Shared/_SelectLanguagePartial.cshtml* позволяет выбрать язык и региональные параметры из списка поддерживаемых:</span><span class="sxs-lookup"><span data-stu-id="39277-649">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="39277-650">Файл *Views/Shared/_SelectLanguagePartial.cshtml* добавляется в раздел `footer` файла макета, поэтому он доступен всем представлениям:</span><span class="sxs-lookup"><span data-stu-id="39277-650">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="39277-651">Метод `SetLanguage` задает файл cookie языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-651">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="39277-652">Подключить файл *_SelectLanguagePartial.cshtml* к образцу кода этого проекта нельзя.</span><span class="sxs-lookup"><span data-stu-id="39277-652">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="39277-653">В проекте **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть код для передачи объекта `RequestLocalizationOptions` в частичное представление Razor посредством контейнера [внедрения зависимостей](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="39277-653">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="39277-654">Данные маршрутов привязки модели и строки запросов</span><span class="sxs-lookup"><span data-stu-id="39277-654">Model binding route data and query strings</span></span>

<span data-ttu-id="39277-655">См. раздел [Поведение глобализации для данных маршрутов привязки модели и строк запросов](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="39277-655">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="39277-656">Термины, относящиеся к глобализации и локализации</span><span class="sxs-lookup"><span data-stu-id="39277-656">Globalization and localization terms</span></span>

<span data-ttu-id="39277-657">Процесс локализации приложения требует базового понимания распространенных кодировок, часто используемых при разработке современного программного обеспечения, и связанных с ними проблем.</span><span class="sxs-lookup"><span data-stu-id="39277-657">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="39277-658">Хотя на всех компьютерах текст сохраняется в виде цифр (кодов), в разных системах эти коды для одного и того же текста различаются.</span><span class="sxs-lookup"><span data-stu-id="39277-658">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="39277-659">Под процессом локализации понимается перевод пользовательского интерфейса приложения для определенного языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-659">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="39277-660">[Возможность локализации](/dotnet/standard/globalization-localization/localizability-review) — это определяемое на промежуточной стадии состояние готовности глобализованного приложения к локализации.</span><span class="sxs-lookup"><span data-stu-id="39277-660">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="39277-661">Формат [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) для названия языка и региональных параметров имеет вид `<languagecode2>-<country/regioncode2>`, где `<languagecode2>` — это код языка, а `<country/regioncode2>` — код субкультуры.</span><span class="sxs-lookup"><span data-stu-id="39277-661">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="39277-662">Примеры: `es-CL` для испанского языка (Чили), `en-US` для английского языка (США), `en-AU` для английского языка (Австралия).</span><span class="sxs-lookup"><span data-stu-id="39277-662">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="39277-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) — это комбинация двухбуквенного кода культуры ISO 639 в нижнем регистре (он связан с языком) и двухбуквенного кода субкультуры ISO 3166 в верхнем регистре (он связан со страной или регионом).</span><span class="sxs-lookup"><span data-stu-id="39277-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="39277-664">См. статью [Имя языка и региональных параметров](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="39277-664">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="39277-665">Вместо слова "интернационализация" (internationalization) часто используют аббревиатуру "I18N".</span><span class="sxs-lookup"><span data-stu-id="39277-665">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="39277-666">В аббревиатуру включаются первая и последняя буквы слова, а также число букв между ними, то есть 18 — это число букв между первой ("I") и последней ("N") буквами.</span><span class="sxs-lookup"><span data-stu-id="39277-666">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="39277-667">То же самое касается глобализации (Globalization — G11N) и локализации (Localization — L10N).</span><span class="sxs-lookup"><span data-stu-id="39277-667">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="39277-668">Термины:</span><span class="sxs-lookup"><span data-stu-id="39277-668">Terms:</span></span>

* <span data-ttu-id="39277-669">Глобализация (G11N): процесс подготовки приложения к поддержке различных языков и регионов.</span><span class="sxs-lookup"><span data-stu-id="39277-669">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="39277-670">Локализация (L10N): процесс настройки приложения для работы с конкретным языком и регионом.</span><span class="sxs-lookup"><span data-stu-id="39277-670">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="39277-671">Интернационализация (I18N): описывает глобализацию и локализацию.</span><span class="sxs-lookup"><span data-stu-id="39277-671">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="39277-672">Язык и региональные параметры: обозначает язык и (необязательно) регион.</span><span class="sxs-lookup"><span data-stu-id="39277-672">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="39277-673">Нейтральный язык и региональные параметры: язык и региональные параметры, для которых указан язык, но не регион.</span><span class="sxs-lookup"><span data-stu-id="39277-673">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="39277-674">(например, "en", "es").</span><span class="sxs-lookup"><span data-stu-id="39277-674">(for example "en", "es")</span></span>
* <span data-ttu-id="39277-675">Конкретный язык и региональные параметры: язык и региональные параметры, для которых указаны язык и регион.</span><span class="sxs-lookup"><span data-stu-id="39277-675">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="39277-676">(например, "en-US", "en-GB", "es-CL").</span><span class="sxs-lookup"><span data-stu-id="39277-676">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="39277-677">Родительский язык и региональные параметры: нейтральные язык и региональные параметры, содержащие конкретные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-677">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="39277-678">(например, "en" — это родительские язык и региональные параметры для "en-US" и "en-GB").</span><span class="sxs-lookup"><span data-stu-id="39277-678">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="39277-679">Языковой стандарт: тот же, что и язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-679">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="39277-680">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="39277-680">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="39277-681">[Проект Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb), используемый в этой статье.</span><span class="sxs-lookup"><span data-stu-id="39277-681">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="39277-682">Глобализация и локализация приложений .NET</span><span class="sxs-lookup"><span data-stu-id="39277-682">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="39277-683">Ресурсы в RESX-файлах</span><span class="sxs-lookup"><span data-stu-id="39277-683">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="39277-684">Набор средств многоязычных приложений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="39277-684">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="39277-685">Локализация и универсальные шаблоны</span><span class="sxs-lookup"><span data-stu-id="39277-685">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="39277-686">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Дэмиен Боуден](https://twitter.com/damien_bod) (Damien Bowden), [Барт Каликсто](https://twitter.com/bartmax) (Bart Calixto), [Надим Афана](https://afana.me/) (Nadeem Afana) и [Хишам Бин Атея](https://twitter.com/hishambinateya) (Hisham Bin Ateya)</span><span class="sxs-lookup"><span data-stu-id="39277-686">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="39277-687">Создание многоязычного веб-сайта позволит расширить аудиторию.</span><span class="sxs-lookup"><span data-stu-id="39277-687">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="39277-688">ASP.NET Core предоставляет службы и ПО промежуточного слоя для локализации на разные языки и для разных региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-688">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="39277-689">Интернационализация предполагает [глобализацию](/dotnet/api/system.globalization) и [локализацию](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="39277-689">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="39277-690">Глобализация — это процесс разработки приложений, которые поддерживают разные языки и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-690">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="39277-691">Глобализация добавляет поддержку ввода отображения и вывода определенного набора языковых сценариев, относящихся к конкретным регионам.</span><span class="sxs-lookup"><span data-stu-id="39277-691">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="39277-692">Локализация — это процесс адаптации глобализованного приложения, уже подготовленного к локализации, к определенному языку, языковому стандарту и региональным параметрам.</span><span class="sxs-lookup"><span data-stu-id="39277-692">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="39277-693">Дополнительные сведения см. в разделе **Термины, относящиеся к глобализации и локализации** ближе к концу этого документа.</span><span class="sxs-lookup"><span data-stu-id="39277-693">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="39277-694">Локализация приложения включает следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="39277-694">App localization involves the following:</span></span>

1. <span data-ttu-id="39277-695">обеспечение возможности локализации для содержимого приложения;</span><span class="sxs-lookup"><span data-stu-id="39277-695">Make the app's content localizable</span></span>
1. <span data-ttu-id="39277-696">предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров;</span><span class="sxs-lookup"><span data-stu-id="39277-696">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="39277-697">реализацию стратегии по выбору языка и региональных параметров для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="39277-697">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="39277-698">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="39277-698">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="39277-699">Обеспечение возможности локализации для содержимого приложения</span><span class="sxs-lookup"><span data-stu-id="39277-699">Make the app's content localizable</span></span>

<span data-ttu-id="39277-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` не требует сохранять строки на языке по умолчанию в файле ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="39277-701">Вы можете разрабатывать приложение, предназначенное для локализации, не создавая файлы ресурсов на ранних этапах разработки.</span><span class="sxs-lookup"><span data-stu-id="39277-701">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="39277-702">В приведенном ниже коде показано, как подготовить строку "About Title" для локализации.</span><span class="sxs-lookup"><span data-stu-id="39277-702">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="39277-703">В предыдущем коде реализация `IStringLocalizer<T>` получена в результате [внедрения зависимостей](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="39277-703">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="39277-704">Если локализованное значение для строки "About Title" не найдено, возвращается ключ индексатора, то есть строка "About Title".</span><span class="sxs-lookup"><span data-stu-id="39277-704">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="39277-705">Вы можете оставить литеральные строки на языке по умолчанию и заключить их в средство локализации, чтобы сосредоточиться на разработке приложения.</span><span class="sxs-lookup"><span data-stu-id="39277-705">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="39277-706">Вы разрабатываете приложение на языке по умолчанию и подготавливаете его к локализации, не создавая предварительно файл ресурсов по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39277-706">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="39277-707">Вы также можете выбрать традиционный подход и предоставить ключ для извлечения строки на языке по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39277-707">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="39277-708">Для многих разработчиков новый рабочий процесс, который не требует наличия файла *RESX* на языке по умолчанию и позволяет просто заключать строки для дальнейшей обработки, может помочь снизить затраты на локализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="39277-708">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="39277-709">Другие разработчики могут предпочесть традиционный рабочий процесс, так как он упрощает работу с более длинными строками и обновление локализованных строк.</span><span class="sxs-lookup"><span data-stu-id="39277-709">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="39277-710">Используйте реализацию `IHtmlLocalizer<T>` для ресурсов, содержащих код HTML.</span><span class="sxs-lookup"><span data-stu-id="39277-710">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="39277-711">Интерфейс `IHtmlLocalizer` кодирует в HTML аргументы, отформатированные в строке ресурса, но не кодирует в HTML саму строку.</span><span class="sxs-lookup"><span data-stu-id="39277-711">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="39277-712">В выделенной ниже строке в HTML кодируется только значение параметра `name`.</span><span class="sxs-lookup"><span data-stu-id="39277-712">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="39277-713">**Примечание.** Как правило, требуется локализовать только текст, но не код HTML.</span><span class="sxs-lookup"><span data-stu-id="39277-713">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="39277-714">На самом нижнем уровне интерфейс `IStringLocalizerFactory` можно получить из [внедрения зависимостей](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="39277-714">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="39277-715">В приведенном выше коде демонстрируются оба фабричных метода создания.</span><span class="sxs-lookup"><span data-stu-id="39277-715">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="39277-716">Вы можете разбивать локализованные строки по контроллеру, области или использовать всего один конвейер.</span><span class="sxs-lookup"><span data-stu-id="39277-716">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="39277-717">В образце приложения для общих ресурсов применяется класс-заглушка с именем `SharedResource`.</span><span class="sxs-lookup"><span data-stu-id="39277-717">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="39277-718">Некоторые разработчики используют класс `Startup` для хранения глобальных или общих строк.</span><span class="sxs-lookup"><span data-stu-id="39277-718">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="39277-719">В примере ниже используются средства локализации `InfoController` и `SharedResource`.</span><span class="sxs-lookup"><span data-stu-id="39277-719">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="39277-720">Локализация представления</span><span class="sxs-lookup"><span data-stu-id="39277-720">View localization</span></span>

<span data-ttu-id="39277-721">Служба `IViewLocalizer` предоставляет локализованные строки для [представления](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="39277-721">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="39277-722">Класс `ViewLocalizer` реализует этот интерфейс и находит расположение ресурсов по пути к файлу представления.</span><span class="sxs-lookup"><span data-stu-id="39277-722">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="39277-723">В следующем примере кода демонстрируется использование реализации `IViewLocalizer` по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="39277-723">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="39277-724">Реализация `IViewLocalizer` по умолчанию находит файл ресурсов по имени файла представления.</span><span class="sxs-lookup"><span data-stu-id="39277-724">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="39277-725">Возможности использовать глобальный общий файл ресурсов нет.</span><span class="sxs-lookup"><span data-stu-id="39277-725">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="39277-726">`ViewLocalizer` реализует средство локализации с помощью интерфейса `IHtmlLocalizer`, поэтому Razor не кодирует локализованную строку в HTML.</span><span class="sxs-lookup"><span data-stu-id="39277-726">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="39277-727">Вы можете параметризовать строки ресурсов, и `IViewLocalizer` будет кодировать в HTML параметры, но не строки ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-727">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="39277-728">Рассмотрим следующую разметку Razor:</span><span class="sxs-lookup"><span data-stu-id="39277-728">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="39277-729">Файл ресурсов на французском языке может содержать следующие ресурсы:</span><span class="sxs-lookup"><span data-stu-id="39277-729">A French resource file could contain the following:</span></span>

| <span data-ttu-id="39277-730">Ключ</span><span class="sxs-lookup"><span data-stu-id="39277-730">Key</span></span> | <span data-ttu-id="39277-731">Значение</span><span class="sxs-lookup"><span data-stu-id="39277-731">Value</span></span> |
| ----- | ---
<span data-ttu-id="39277-732">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-732">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-733">'Blazor'</span></span>
- <span data-ttu-id="39277-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-734">'Identity'</span></span>
- <span data-ttu-id="39277-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-736">'Razor'</span></span>
- <span data-ttu-id="39277-737">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-737">'SignalR' uid:</span></span> 

<span data-ttu-id="39277-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="39277-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="39277-739">Преобразованное для просмотра представление будет содержать разметку HTML из файла ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-739">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="39277-740">**Примечание.** Как правило, требуется локализовать только текст, но не код HTML.</span><span class="sxs-lookup"><span data-stu-id="39277-740">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="39277-741">Чтобы использовать в представлении общий файл ресурсов, внедрите `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="39277-741">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="39277-742">Локализация DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="39277-742">DataAnnotations localization</span></span>

<span data-ttu-id="39277-743">Сообщения об ошибках DataAnnotations локализуются с помощью `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="39277-743">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="39277-744">При использовании параметра `ResourcesPath = "Resources"` сообщения об ошибках в `RegisterViewModel` могут сохраняться по одному из следующих путей:</span><span class="sxs-lookup"><span data-stu-id="39277-744">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="39277-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="39277-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="39277-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="39277-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="39277-747">В ASP.NET Core MVC 1.1.0 и более поздних версиях атрибуты, не относящиеся в проверке, локализуются.</span><span class="sxs-lookup"><span data-stu-id="39277-747">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="39277-748">В ASP.NET Core MVC 1.0 **не** производится поиск локализованных строк для атрибутов, не относящихся к проверке.</span><span class="sxs-lookup"><span data-stu-id="39277-748">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="39277-749">Использование одной строки ресурса для нескольких классов</span><span class="sxs-lookup"><span data-stu-id="39277-749">Using one resource string for multiple classes</span></span>

<span data-ttu-id="39277-750">В следующем коде показано, как использовать одну строку ресурса для атрибутов проверки с несколькими классами:</span><span class="sxs-lookup"><span data-stu-id="39277-750">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="39277-751">В предыдущем коде `SharedResource` — это класс, соответствующий файлу RESX, в котором хранятся сообщения о проверке.</span><span class="sxs-lookup"><span data-stu-id="39277-751">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="39277-752">При таком подходе DataAnnotations будут использовать только `SharedResource`, а не ресурс для каждого класса.</span><span class="sxs-lookup"><span data-stu-id="39277-752">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="39277-753">Предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="39277-753">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="39277-754">SupportedCultures и SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="39277-754">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="39277-755">ASP.NET Core позволяет указывать два значения языка и региональных параметров: `SupportedCultures` и `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="39277-755">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="39277-756">Объект [CultureInfo](/dotnet/api/system.globalization.cultureinfo) для `SupportedCultures` определяет результаты функций, зависящих от языка и региональных параметров: форматирование дат, времени, чисел, денежных единиц и т. д.</span><span class="sxs-lookup"><span data-stu-id="39277-756">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="39277-757">`SupportedCultures` также определяет порядок сортировки текста, соглашения о регистре символов и способы сравнения строк.</span><span class="sxs-lookup"><span data-stu-id="39277-757">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="39277-758">Дополнительные сведения о получении сервером значения языка и региональных параметров см. в описании свойства [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="39277-758">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="39277-759">Значение `SupportedUICultures` определяет то, какие строки переводов (в файле *RESX*) ищет объект [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="39277-759">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="39277-760">`ResourceManager` ищет связанные с языком и региональными параметрами строки, которые определяются значением `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="39277-760">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="39277-761">Каждый поток в .NET имеет объекты `CurrentCulture` и `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="39277-761">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="39277-762">ASP.NET Core проверяет эти значения при обработке функций, зависящих от языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-762">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="39277-763">Например, если для текущего потока заданы язык и региональные параметры "en-US" (английский, США), метод `DateTime.Now.ToLongDateString()` выводит строку "Thursday, February 18, 2016", но если `CurrentCulture` имеет значение "ru-RU" (русский, Россия), выводится строка "четверг, 18 февраля 2016 г."</span><span class="sxs-lookup"><span data-stu-id="39277-763">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="39277-764">Файлы ресурсов</span><span class="sxs-lookup"><span data-stu-id="39277-764">Resource files</span></span>

<span data-ttu-id="39277-765">Файл ресурсов — это полезное средство для отделения локализуемых строк от кода.</span><span class="sxs-lookup"><span data-stu-id="39277-765">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="39277-766">Переведенные строки на языках, отличных от языка по умолчанию, содержатся в отдельных файлах ресурсов с расширением *RESX*.</span><span class="sxs-lookup"><span data-stu-id="39277-766">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="39277-767">Например, вам нужно создать файл ресурсов для испанского языка с именем *Welcome.es.resx*, который будет содержать переведенные строки.</span><span class="sxs-lookup"><span data-stu-id="39277-767">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="39277-768">"es" — это код испанского языка.</span><span class="sxs-lookup"><span data-stu-id="39277-768">"es" is the language code for Spanish.</span></span> <span data-ttu-id="39277-769">Чтобы создать этот файл ресурсов в Visual Studio, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="39277-769">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="39277-770">В **обозревателе решений** щелкните правой кнопкой мыши папку, которая будет содержать файл ресурсов, а затем выберите пункты **Добавить** > **Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="39277-770">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Вложенное контекстное меню: в обозревателе решений контекстное меню открывается для папки ресурсов.](localization/_static/newi.png)

2. <span data-ttu-id="39277-773">В поле **Поиск установленных шаблонов** введите слово "ресурс" и укажите имя файла.</span><span class="sxs-lookup"><span data-stu-id="39277-773">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Диалоговое окно ''Добавление нового элемента''](localization/_static/res.png)

3. <span data-ttu-id="39277-775">Введите значение ключа (строку на исходном языке) в столбце **Имя** и переведенную строку в столбце **Значение**.</span><span class="sxs-lookup"><span data-stu-id="39277-775">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Файл Welcome.es.resx (файлов ресурсов с приветствиями на испанском языке) со словом "Hello" в столбце "Имя" и словом "Hol"a ("Hello" на испанском) в столбце "Значение"](localization/_static/hola.png)

    <span data-ttu-id="39277-777">В Visual Studio отобразится файл *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-777">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Обозреватель решений с файлом ресурсов на испанском языке](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="39277-779">Именование файлов ресурсов</span><span class="sxs-lookup"><span data-stu-id="39277-779">Resource file naming</span></span>

<span data-ttu-id="39277-780">Имена ресурсов представляют собой полные имена типов соответствующего класса за исключением имени сборки.</span><span class="sxs-lookup"><span data-stu-id="39277-780">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="39277-781">Например, ресурс на французском языке в проекте, главная сборка которого имеет имя `LocalizationWebsite.Web.dll`, для класса `LocalizationWebsite.Web.Startup` будет иметь имя *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-781">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="39277-782">Ресурс для класса `LocalizationWebsite.Web.Controllers.HomeController` будет иметь имя *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-782">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="39277-783">Если пространство имен целевого класса не совпадает с именем сборки, необходимо использовать полное имя типа.</span><span class="sxs-lookup"><span data-stu-id="39277-783">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="39277-784">Например, в образце проекта ресурс для типа `ExtraNamespace.Tools` будет иметь имя *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-784">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="39277-785">В образце проекта метод `ConfigureServices` присваивает свойству `ResourcesPath` значение "Resources", поэтому относительный путь к файлу ресурсов на французском языке для контроллера домашней страницы в проекте будет иметь вид *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-785">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="39277-786">Кроме того, для упорядочения файлов ресурсов можно использовать папки.</span><span class="sxs-lookup"><span data-stu-id="39277-786">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="39277-787">Для контроллера домашней страницы путь будет иметь вид *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-787">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="39277-788">Если параметр `ResourcesPath` не используется, файл *RESX* будет находиться в базовом каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="39277-788">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="39277-789">Файл ресурса для `HomeController` будет иметь имя *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="39277-789">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="39277-790">Выбор соглашения об именовании на основе точечной нотации или пути зависит от того, как следует упорядочивать файлы ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-790">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="39277-791">Имя ресурса</span><span class="sxs-lookup"><span data-stu-id="39277-791">Resource name</span></span> | <span data-ttu-id="39277-792">Точечная нотация или путь</span><span class="sxs-lookup"><span data-stu-id="39277-792">Dot or path naming</span></span> |
| ---
<span data-ttu-id="39277-793">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-793">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-794">'Blazor'</span></span>
- <span data-ttu-id="39277-795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-795">'Identity'</span></span>
- <span data-ttu-id="39277-796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-796">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-797">'Razor'</span></span>
- <span data-ttu-id="39277-798">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-799">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-799">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-800">'Blazor'</span></span>
- <span data-ttu-id="39277-801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-801">'Identity'</span></span>
- <span data-ttu-id="39277-802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-802">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-803">'Razor'</span></span>
- <span data-ttu-id="39277-804">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-805">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-805">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-806">'Blazor'</span></span>
- <span data-ttu-id="39277-807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-807">'Identity'</span></span>
- <span data-ttu-id="39277-808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-808">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-809">'Razor'</span></span>
- <span data-ttu-id="39277-810">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-811">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-811">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-812">'Blazor'</span></span>
- <span data-ttu-id="39277-813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-813">'Identity'</span></span>
- <span data-ttu-id="39277-814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-814">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-815">'Razor'</span></span>
- <span data-ttu-id="39277-816">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-816">'SignalR' uid:</span></span> 

<span data-ttu-id="39277-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-818">'Blazor'</span></span>
- <span data-ttu-id="39277-819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-819">'Identity'</span></span>
- <span data-ttu-id="39277-820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-820">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-821">'Razor'</span></span>
- <span data-ttu-id="39277-822">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-823">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-823">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-824">'Blazor'</span></span>
- <span data-ttu-id="39277-825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-825">'Identity'</span></span>
- <span data-ttu-id="39277-826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-826">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-827">'Razor'</span></span>
- <span data-ttu-id="39277-828">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-829">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-829">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-830">'Blazor'</span></span>
- <span data-ttu-id="39277-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-831">'Identity'</span></span>
- <span data-ttu-id="39277-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-833">'Razor'</span></span>
- <span data-ttu-id="39277-834">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39277-835">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39277-835">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="39277-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39277-836">'Blazor'</span></span>
- <span data-ttu-id="39277-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39277-837">'Identity'</span></span>
- <span data-ttu-id="39277-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39277-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="39277-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39277-839">'Razor'</span></span>
- <span data-ttu-id="39277-840">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="39277-840">'SignalR' uid:</span></span> 

<span data-ttu-id="39277-841">------- | | Resources/Controllers.HomeController.fr.resx | Точка  | | Resources/Controllers/HomeController.fr.resx  | Путь | |    |     |</span><span class="sxs-lookup"><span data-stu-id="39277-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="39277-842">Файлы ресурсов, для которых используется директива `@inject IViewLocalizer` в представлениях Razor, следуют той же модели.</span><span class="sxs-lookup"><span data-stu-id="39277-842">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="39277-843">Файлу ресурсов для представления может присваиваться имя на основе либо точечной нотации, либо пути.</span><span class="sxs-lookup"><span data-stu-id="39277-843">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="39277-844">В файле ресурсов для представления Razor имитируется путь к связанному файлу представления.</span><span class="sxs-lookup"><span data-stu-id="39277-844">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="39277-845">Допустим, свойству `ResourcesPath` присвоено значение "Resources". В этом случае файл ресурсов на французском языке, связанный с представлением *Views/Home/About.cshtml*, может иметь одно из следующих имен:</span><span class="sxs-lookup"><span data-stu-id="39277-845">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="39277-846">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="39277-846">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="39277-847">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="39277-847">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="39277-848">Если параметр `ResourcesPath` не используется, файл *RESX* для представления будет находиться в той же папке, что и представление.</span><span class="sxs-lookup"><span data-stu-id="39277-848">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="39277-849">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="39277-849">RootNamespaceAttribute</span></span> 

<span data-ttu-id="39277-850">Атрибут [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) содержит корневое пространство имен сборки, если корневое пространство имен сборки отличается от имени сборки.</span><span class="sxs-lookup"><span data-stu-id="39277-850">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="39277-851">Это может произойти, если имя проекта — недопустимый идентификатор .NET.</span><span class="sxs-lookup"><span data-stu-id="39277-851">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="39277-852">Например, `my-project-name.csproj` будет использовать корневое пространство имен `my_project_name` и имя сборки `my-project-name`, что повлечет эту ошибку.</span><span class="sxs-lookup"><span data-stu-id="39277-852">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="39277-853">Если корневое пространство имен сборки отличается от имени сборки</span><span class="sxs-lookup"><span data-stu-id="39277-853">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="39277-854">Локализация не работает по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39277-854">Localization does not work by default.</span></span>
* <span data-ttu-id="39277-855">Локализация завершается сбоем из-за метода поиска ресурсов в сборке.</span><span class="sxs-lookup"><span data-stu-id="39277-855">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="39277-856">`RootNamespace` — это значение во время сборки, которое недоступно выполняющемуся процессу.</span><span class="sxs-lookup"><span data-stu-id="39277-856">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="39277-857">Если `RootNamespace` отличается от `AssemblyName`, включите следующее в файл *AssemblyInfo.cs* (со значениями параметров, замененными фактическими значениями).</span><span class="sxs-lookup"><span data-stu-id="39277-857">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="39277-858">Приведенный выше код обеспечивает успешное разрешение RESX-файлов.</span><span class="sxs-lookup"><span data-stu-id="39277-858">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="39277-859">Резервный язык и региональные параметры</span><span class="sxs-lookup"><span data-stu-id="39277-859">Culture fallback behavior</span></span>

<span data-ttu-id="39277-860">При поиске ресурса локализации использует резервный язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-860">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="39277-861">Если запрошенный язык и региональные параметры не найдены, используется родительский язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-861">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="39277-862">Кстати, родительский язык и региональные параметры представляют свойство [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent).</span><span class="sxs-lookup"><span data-stu-id="39277-862">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="39277-863">Обычно (но не всегда) это означает удаление национального символа из ISO.</span><span class="sxs-lookup"><span data-stu-id="39277-863">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="39277-864">Например, диалект испанского в Мексике — es-MX.</span><span class="sxs-lookup"><span data-stu-id="39277-864">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="39277-865">Родительский элемент "es" (испанский) не относится к той или иной стране.</span><span class="sxs-lookup"><span data-stu-id="39277-865">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="39277-866">Допустим, сайт получает запрос на ресурс "Приветствие" с использованием языка и региональных параметров fr-CA.</span><span class="sxs-lookup"><span data-stu-id="39277-866">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="39277-867">Система локализации ищет в следующих ресурсах по порядку и выбирает первое совпадение:</span><span class="sxs-lookup"><span data-stu-id="39277-867">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="39277-868">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="39277-868">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="39277-869">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="39277-869">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="39277-870">*Welcome.resx* (если `NeutralResourcesLanguage` — fr-CA)</span><span class="sxs-lookup"><span data-stu-id="39277-870">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="39277-871">Если вы удалите указатель языка и региональных параметров ".fr" и при этом задан французский язык и региональные параметры, будет считан файл ресурсов по умолчанию и строки локализуются.</span><span class="sxs-lookup"><span data-stu-id="39277-871">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="39277-872">Диспетчер ресурсов назначает ресурс по умолчанию или резервный ресурс на тот случай, когда соответствия запрошенному языку и региональным параметрам не найдено.</span><span class="sxs-lookup"><span data-stu-id="39277-872">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="39277-873">Если нужно, чтобы при отсутствии ресурса для запрошенного языка и региональных параметров просто возвращался ключ, не используйте файл ресурсов по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39277-873">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="39277-874">Создание файлов ресурсов с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39277-874">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="39277-875">Если вы создаете в Visual Studio файл ресурсов, в имени которого не указаны язык и региональные параметры (например, *Welcome.resx*), Visual Studio создаст класс C# со свойством для каждой строки.</span><span class="sxs-lookup"><span data-stu-id="39277-875">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="39277-876">В ASP.NET Core обычно требуется иное поведение.</span><span class="sxs-lookup"><span data-stu-id="39277-876">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="39277-877">Как правило, файл ресурсов *RESX* по умолчанию (файл *RESX* без указания языка и региональных параметров) не используется.</span><span class="sxs-lookup"><span data-stu-id="39277-877">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="39277-878">Мы рекомендуем создать файл *RESX* с указанием языка и региональных параметров (например, *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="39277-878">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="39277-879">При создании файла *RESX* с указанием языка и региональных параметров среда Visual Studio не создает файл класса.</span><span class="sxs-lookup"><span data-stu-id="39277-879">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="39277-880">Добавление других языков и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="39277-880">Add other cultures</span></span>

<span data-ttu-id="39277-881">Каждое сочетание языка и региональных параметров (кроме языка по умолчанию) требует уникального файла ресурсов.</span><span class="sxs-lookup"><span data-stu-id="39277-881">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="39277-882">Создавая файлы ресурсов для разных языков, языковых стандартов и региональных параметров, вы включаете в их имена коды языков ISO (например, **en-us**, **ru-ru** и **fr-ca**).</span><span class="sxs-lookup"><span data-stu-id="39277-882">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="39277-883">Эти коды ISO помещаются между именем файла и расширением *RESX*, например *Welcome.es-MX.resx* (испанский, Мексика).</span><span class="sxs-lookup"><span data-stu-id="39277-883">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="39277-884">реализацию стратегии по выбору языка и региональных параметров для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="39277-884">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="39277-885">Настройка локализации</span><span class="sxs-lookup"><span data-stu-id="39277-885">Configure localization</span></span>

<span data-ttu-id="39277-886">Локализация настраивается в методе `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="39277-886">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="39277-887">Метод `AddLocalization` добавляет службы локализации в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="39277-887">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="39277-888">В приведенном выше коде также задается путь к ресурсам "Resources".</span><span class="sxs-lookup"><span data-stu-id="39277-888">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="39277-889">Метод `AddViewLocalization` добавляет поддержку файлов локализованных представлений.</span><span class="sxs-lookup"><span data-stu-id="39277-889">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="39277-890">В этом примере локализация образца представления основана на суффиксе файла представления,</span><span class="sxs-lookup"><span data-stu-id="39277-890">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="39277-891">например "fr" в файле *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="39277-891">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="39277-892">Метод `AddDataAnnotationsLocalization` добавляет поддержку локализованных сообщений проверки `DataAnnotations` посредством абстракций `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="39277-892">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="39277-893">Промежуточный слой локализации</span><span class="sxs-lookup"><span data-stu-id="39277-893">Localization middleware</span></span>

<span data-ttu-id="39277-894">Текущий язык и региональные параметры в запросе задаются в [промежуточном слое](xref:fundamentals/middleware/index) локализации.</span><span class="sxs-lookup"><span data-stu-id="39277-894">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="39277-895">Промежуточный слой локализации включается в методе `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="39277-895">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="39277-896">Промежуточный слой локализации должен настраиваться перед другими промежуточными слоями, которые могут проверять язык и региональные параметры запроса (например, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="39277-896">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="39277-897">`UseRequestLocalization` инициализирует объект `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="39277-897">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="39277-898">При каждом запросе проверяется список поставщиков `RequestCultureProvider` в объекте `RequestLocalizationOptions` и используется первый поставщик, который может успешно определить язык и региональные параметры запроса.</span><span class="sxs-lookup"><span data-stu-id="39277-898">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="39277-899">Поставщики по умолчанию берутся из класса `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="39277-899">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="39277-900">В списке по умолчанию поставщики следуют от наиболее конкретных до наиболее общих.</span><span class="sxs-lookup"><span data-stu-id="39277-900">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="39277-901">Далее в этой статье вы узнаете, как можно изменить этот порядок и даже добавить пользовательский поставщик языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-901">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="39277-902">Если ни один из поставщиков не может определить язык и региональные параметры запроса, используется `DefaultRequestCulture`.</span><span class="sxs-lookup"><span data-stu-id="39277-902">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="39277-903">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="39277-903">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="39277-904">Некоторые приложения используют строку запроса для указания [языка и региональных параметров самого приложения и пользовательского интерфейса](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="39277-904">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="39277-905">Если в приложении для этого применяется файл cookie или заголовок Accept-Language, добавление строки запроса к URL-адресу может быть полезным в целях отладки и тестирования кода.</span><span class="sxs-lookup"><span data-stu-id="39277-905">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="39277-906">По умолчанию поставщик `QueryStringRequestCultureProvider` регистрируется в качестве первого поставщика локализации в списке `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="39277-906">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="39277-907">Вы передаете параметры строки запроса `culture` и `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="39277-907">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="39277-908">В следующем примере в качестве языка и региональных параметров (язык и регион) задаются испанский язык и Мексика:</span><span class="sxs-lookup"><span data-stu-id="39277-908">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="39277-909">Если передан только один из двух параметров (`culture` или `ui-culture`), поставщик строки запроса задаст с его помощью оба значения.</span><span class="sxs-lookup"><span data-stu-id="39277-909">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="39277-910">Например, если передан только язык и региональные параметры, будут заданы оба значения `Culture` и `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="39277-910">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="39277-911">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="39277-911">CookieRequestCultureProvider</span></span>

<span data-ttu-id="39277-912">Рабочие приложения часто предоставляют механизм для указания языка и региональных параметров с помощью соответствующего файла cookie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="39277-912">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="39277-913">Чтобы создать файл cookie, используйте метод `MakeCookieValue`.</span><span class="sxs-lookup"><span data-stu-id="39277-913">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="39277-914">`CookieRequestCultureProvider` `DefaultCookieName` возвращает имя файла cookie по умолчанию, с помощью которого отслеживается предпочтительный язык и региональные параметры пользователя.</span><span class="sxs-lookup"><span data-stu-id="39277-914">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="39277-915">Имя файла cookie по умолчанию — `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="39277-915">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="39277-916">Файл cookie имеет формат `c=%LANGCODE%|uic=%LANGCODE%`, где `c` — это `Culture`, а `uic` — это `UICulture`, например:</span><span class="sxs-lookup"><span data-stu-id="39277-916">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="39277-917">Если указаны либо только язык и региональные параметры, либо только язык и региональные параметры пользовательского интерфейса, это значение будет использоваться для обоих параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-917">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="39277-918">Заголовок HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="39277-918">The Accept-Language HTTP header</span></span>

<span data-ttu-id="39277-919">[Заголовок Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) может задаваться в большинстве браузеров и изначально предназначался для указания языка пользователя.</span><span class="sxs-lookup"><span data-stu-id="39277-919">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="39277-920">Он показывает, какой язык был выбран в браузере или унаследован от базовой операционной системы.</span><span class="sxs-lookup"><span data-stu-id="39277-920">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="39277-921">Заголовок HTTP Accept-Language в запросе из браузера — это не самый надежный способ определения предпочтительного языка пользователя (см. статью [Настройка языковых предпочтений в браузере](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="39277-921">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="39277-922">В рабочем приложении должна быть возможность выбора языка и региональных параметров пользователем.</span><span class="sxs-lookup"><span data-stu-id="39277-922">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="39277-923">Задание заголовка HTTP Accept-Language в Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="39277-923">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="39277-924">Щелкните значок шестеренки и выберите пункт **Свойства браузера**.</span><span class="sxs-lookup"><span data-stu-id="39277-924">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="39277-925">Нажмите кнопку **Языки**.</span><span class="sxs-lookup"><span data-stu-id="39277-925">Tap **Languages**.</span></span>

    ![Свойства браузера](localization/_static/lang.png)

3. <span data-ttu-id="39277-927">Нажмите **Задать предпочитаемые языки**.</span><span class="sxs-lookup"><span data-stu-id="39277-927">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="39277-928">Нажмите **Добавить язык**.</span><span class="sxs-lookup"><span data-stu-id="39277-928">Tap **Add a language**.</span></span>

5. <span data-ttu-id="39277-929">Добавьте язык.</span><span class="sxs-lookup"><span data-stu-id="39277-929">Add the language.</span></span>

6. <span data-ttu-id="39277-930">Выберите язык, а затем нажмите кнопку **Вверх**.</span><span class="sxs-lookup"><span data-stu-id="39277-930">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="39277-931">Заголовок HTTP Content-Language</span><span class="sxs-lookup"><span data-stu-id="39277-931">The Content-Language HTTP header</span></span>

<span data-ttu-id="39277-932">Заголовок сущности [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language):</span><span class="sxs-lookup"><span data-stu-id="39277-932">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="39277-933">Используется для описания языков, предназначенных для аудитории.</span><span class="sxs-lookup"><span data-stu-id="39277-933">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="39277-934">Позволяет пользователю различать в соответствии с предпочтительным языком пользователя.</span><span class="sxs-lookup"><span data-stu-id="39277-934">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="39277-935">Заголовки сущностей используются как в HTTP-запросах, так и в ответах.</span><span class="sxs-lookup"><span data-stu-id="39277-935">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="39277-936">Заголовок `Content-Language` можно добавить, задав свойство `ApplyCurrentCultureToResponseHeaders`.</span><span class="sxs-lookup"><span data-stu-id="39277-936">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="39277-937">Добавление заголовка `Content-Language`:</span><span class="sxs-lookup"><span data-stu-id="39277-937">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="39277-938">Позволяет RequestLocalizationMiddleware задать заголовок `Content-Language` с `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="39277-938">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="39277-939">Устраняет необходимость явной установки заголовка ответа `Content-Language`.</span><span class="sxs-lookup"><span data-stu-id="39277-939">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="39277-940">Использование пользовательского поставщика</span><span class="sxs-lookup"><span data-stu-id="39277-940">Use a custom provider</span></span>

<span data-ttu-id="39277-941">Предположим, необходимо, чтобы ваши клиенты могли сохранять информацию о своем языке и региональных параметрах в ваших базах данных.</span><span class="sxs-lookup"><span data-stu-id="39277-941">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="39277-942">Можно написать поставщик, который будет искать эти значения для пользователя.</span><span class="sxs-lookup"><span data-stu-id="39277-942">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="39277-943">В следующем примере кода показано, как добавить пользовательский поставщик:</span><span class="sxs-lookup"><span data-stu-id="39277-943">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="39277-944">Для добавления или удаления поставщиков локализации используйте объект `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="39277-944">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="39277-945">Задание языка и региональных параметров программным образом</span><span class="sxs-lookup"><span data-stu-id="39277-945">Set the culture programmatically</span></span>

<span data-ttu-id="39277-946">В образце проекта **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть пользовательский интерфейс для задания значения `Culture`.</span><span class="sxs-lookup"><span data-stu-id="39277-946">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="39277-947">Файл *Views/Shared/_SelectLanguagePartial.cshtml* позволяет выбрать язык и региональные параметры из списка поддерживаемых:</span><span class="sxs-lookup"><span data-stu-id="39277-947">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="39277-948">Файл *Views/Shared/_SelectLanguagePartial.cshtml* добавляется в раздел `footer` файла макета, поэтому он доступен всем представлениям:</span><span class="sxs-lookup"><span data-stu-id="39277-948">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="39277-949">Метод `SetLanguage` задает файл cookie языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-949">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="39277-950">Подключить файл *_SelectLanguagePartial.cshtml* к образцу кода этого проекта нельзя.</span><span class="sxs-lookup"><span data-stu-id="39277-950">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="39277-951">В проекте **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть код для передачи объекта `RequestLocalizationOptions` в частичное представление Razor посредством контейнера [внедрения зависимостей](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="39277-951">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="39277-952">Данные маршрутов привязки модели и строки запросов</span><span class="sxs-lookup"><span data-stu-id="39277-952">Model binding route data and query strings</span></span>

<span data-ttu-id="39277-953">См. раздел [Поведение глобализации для данных маршрутов привязки модели и строк запросов](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="39277-953">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="39277-954">Термины, относящиеся к глобализации и локализации</span><span class="sxs-lookup"><span data-stu-id="39277-954">Globalization and localization terms</span></span>

<span data-ttu-id="39277-955">Процесс локализации приложения требует базового понимания распространенных кодировок, часто используемых при разработке современного программного обеспечения, и связанных с ними проблем.</span><span class="sxs-lookup"><span data-stu-id="39277-955">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="39277-956">Хотя на всех компьютерах текст сохраняется в виде цифр (кодов), в разных системах эти коды для одного и того же текста различаются.</span><span class="sxs-lookup"><span data-stu-id="39277-956">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="39277-957">Под процессом локализации понимается перевод пользовательского интерфейса приложения для определенного языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="39277-957">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="39277-958">[Возможность локализации](/dotnet/standard/globalization-localization/localizability-review) — это определяемое на промежуточной стадии состояние готовности глобализованного приложения к локализации.</span><span class="sxs-lookup"><span data-stu-id="39277-958">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="39277-959">Формат [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) для названия языка и региональных параметров имеет вид `<languagecode2>-<country/regioncode2>`, где `<languagecode2>` — это код языка, а `<country/regioncode2>` — код субкультуры.</span><span class="sxs-lookup"><span data-stu-id="39277-959">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="39277-960">Примеры: `es-CL` для испанского языка (Чили), `en-US` для английского языка (США), `en-AU` для английского языка (Австралия).</span><span class="sxs-lookup"><span data-stu-id="39277-960">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="39277-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) — это комбинация двухбуквенного кода культуры ISO 639 в нижнем регистре (он связан с языком) и двухбуквенного кода субкультуры ISO 3166 в верхнем регистре (он связан со страной или регионом).</span><span class="sxs-lookup"><span data-stu-id="39277-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="39277-962">См. статью [Имя языка и региональных параметров](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="39277-962">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="39277-963">Вместо слова "интернационализация" (internationalization) часто используют аббревиатуру "I18N".</span><span class="sxs-lookup"><span data-stu-id="39277-963">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="39277-964">В аббревиатуру включаются первая и последняя буквы слова, а также число букв между ними, то есть 18 — это число букв между первой ("I") и последней ("N") буквами.</span><span class="sxs-lookup"><span data-stu-id="39277-964">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="39277-965">То же самое касается глобализации (Globalization — G11N) и локализации (Localization — L10N).</span><span class="sxs-lookup"><span data-stu-id="39277-965">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="39277-966">Термины:</span><span class="sxs-lookup"><span data-stu-id="39277-966">Terms:</span></span>

* <span data-ttu-id="39277-967">Глобализация (G11N): процесс подготовки приложения к поддержке различных языков и регионов.</span><span class="sxs-lookup"><span data-stu-id="39277-967">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="39277-968">Локализация (L10N): процесс настройки приложения для работы с конкретным языком и регионом.</span><span class="sxs-lookup"><span data-stu-id="39277-968">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="39277-969">Интернационализация (I18N): описывает глобализацию и локализацию.</span><span class="sxs-lookup"><span data-stu-id="39277-969">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="39277-970">Язык и региональные параметры: обозначает язык и (необязательно) регион.</span><span class="sxs-lookup"><span data-stu-id="39277-970">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="39277-971">Нейтральный язык и региональные параметры: язык и региональные параметры, для которых указан язык, но не регион.</span><span class="sxs-lookup"><span data-stu-id="39277-971">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="39277-972">(например, "en", "es").</span><span class="sxs-lookup"><span data-stu-id="39277-972">(for example "en", "es")</span></span>
* <span data-ttu-id="39277-973">Конкретный язык и региональные параметры: язык и региональные параметры, для которых указаны язык и регион.</span><span class="sxs-lookup"><span data-stu-id="39277-973">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="39277-974">(например, "en-US", "en-GB", "es-CL").</span><span class="sxs-lookup"><span data-stu-id="39277-974">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="39277-975">Родительский язык и региональные параметры: нейтральные язык и региональные параметры, содержащие конкретные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-975">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="39277-976">(например, "en" — это родительские язык и региональные параметры для "en-US" и "en-GB").</span><span class="sxs-lookup"><span data-stu-id="39277-976">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="39277-977">Языковой стандарт: тот же, что и язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="39277-977">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="39277-978">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="39277-978">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="39277-979">[Проект Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb), используемый в этой статье.</span><span class="sxs-lookup"><span data-stu-id="39277-979">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="39277-980">Глобализация и локализация приложений .NET</span><span class="sxs-lookup"><span data-stu-id="39277-980">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="39277-981">Ресурсы в RESX-файлах</span><span class="sxs-lookup"><span data-stu-id="39277-981">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="39277-982">Набор средств многоязычных приложений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="39277-982">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="39277-983">Локализация и универсальные шаблоны</span><span class="sxs-lookup"><span data-stu-id="39277-983">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
