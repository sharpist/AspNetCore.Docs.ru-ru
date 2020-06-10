---
title: 'title: Глобализация и локализация в ASP.NET Core author: rick-anderson description: Сведения о службах и ПО промежуточного слоя, предоставляемых ASP.NET Core для локализации содержимого на разные языки и для разных региональных параметров.'
author: rick-anderson
description: 'ms.author: riande ms.date: 30.11.2019 no-loc:'
ms.author: riande
ms.date: 11/30/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization
ms.openlocfilehash: e3b73a7a559d2f4a0803dc26dd42257c60fab884
ms.sourcegitcommit: e95b90585a9bc353f57643ed7d8e652dc498359b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84356964"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>'Blazor'

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

'Identity'

'Let's Encrypt' 'Razor'

'SignalR' uid: fundamentals/localization Глобализация и локализация в ASP.NET Core Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Дэмиен Боуден](https://twitter.com/damien_bod) (Damien Bowden), [Барт Каликсто](https://twitter.com/bartmax) (Bart Calixto), [Надим Афана](https://afana.me/) (Nadeem Afana) и [Хишам Бин Атея](https://twitter.com/hishambinateya) (Hisham Bin Ateya)

Создание многоязычного веб-сайта позволит расширить аудиторию. ASP.NET Core предоставляет службы и ПО промежуточного слоя для локализации на разные языки и для разных региональных параметров.

Интернационализация предполагает [глобализацию](/dotnet/api/system.globalization) и [локализацию](/dotnet/standard/globalization-localization/localization).

1. Глобализация — это процесс разработки приложений, которые поддерживают разные языки и региональные параметры.
1. Глобализация добавляет поддержку ввода отображения и вывода определенного набора языковых сценариев, относящихся к конкретным регионам.
1. Локализация — это процесс адаптации глобализованного приложения, уже подготовленного к локализации, к определенному языку, языковому стандарту и региональным параметрам.

Дополнительные сведения см. в разделе **Термины, относящиеся к глобализации и локализации** ближе к концу этого документа.

## <a name="make-the-apps-content-localizable"></a>Локализация приложения включает следующие задачи:

обеспечение возможности локализации для содержимого приложения; предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров; реализацию стратегии по выбору языка и региональных параметров для каждого запроса. [Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([как скачивать](xref:index#how-to-download-a-sample)) Обеспечение возможности локализации для содержимого приложения <xref:Microsoft.Extensions.Localization.IStringLocalizer> и <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> создавались для повышения производительность при разработке локализованных приложений.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

`IStringLocalizer` использует классы <xref:System.Resources.ResourceManager> и <xref:System.Resources.ResourceReader> для предоставления ресурсов, связанных с определенным языком и региональными параметрами, во время выполнения. Этот интерфейс имеет индексатор и интерфейс `IEnumerable` для возврата локализованных строк. `IStringLocalizer` не требует сохранять строки на языке по умолчанию в файле ресурсов. Вы можете разрабатывать приложение, предназначенное для локализации, не создавая файлы ресурсов на ранних этапах разработки. В приведенном ниже коде показано, как подготовить строку "About Title" для локализации. В предыдущем коде реализация `IStringLocalizer<T>` получена в результате [внедрения зависимостей](dependency-injection.md). Если локализованное значение для строки "About Title" не найдено, возвращается ключ индексатора, то есть строка "About Title".

Вы можете оставить литеральные строки на языке по умолчанию и заключить их в средство локализации, чтобы сосредоточиться на разработке приложения. Вы разрабатываете приложение на языке по умолчанию и подготавливаете его к локализации, не создавая предварительно файл ресурсов по умолчанию. Вы также можете выбрать традиционный подход и предоставить ключ для извлечения строки на языке по умолчанию.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

Для многих разработчиков новый рабочий процесс, который не требует наличия файла *RESX* на языке по умолчанию и позволяет просто заключать строки для дальнейшей обработки, может помочь снизить затраты на локализацию приложения.

Другие разработчики могут предпочесть традиционный рабочий процесс, так как он упрощает работу с более длинными строками и обновление локализованных строк.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Используйте реализацию `IHtmlLocalizer<T>` для ресурсов, содержащих код HTML.

Интерфейс `IHtmlLocalizer` кодирует в HTML аргументы, отформатированные в строке ресурса, но не кодирует в HTML саму строку. В выделенной ниже строке в HTML кодируется только значение параметра `name`.

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

**Примечание.** Как правило, требуется локализовать только текст, но не код HTML. На самом нижнем уровне интерфейс `IStringLocalizerFactory` можно получить из [внедрения зависимостей](dependency-injection.md):

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>В приведенном выше коде демонстрируются оба фабричных метода создания.

Вы можете разбивать локализованные строки по контроллеру, области или использовать всего один конвейер. В образце приложения для общих ресурсов применяется класс-заглушка с именем `SharedResource`. Некоторые разработчики используют класс `Startup` для хранения глобальных или общих строк.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

В примере ниже используются средства локализации `InfoController` и `SharedResource`. Локализация представления Служба `IViewLocalizer` предоставляет локализованные строки для [представления](xref:mvc/views/overview). Класс `ViewLocalizer` реализует этот интерфейс и находит расположение ресурсов по пути к файлу представления. В следующем примере кода демонстрируется использование реализации `IViewLocalizer` по умолчанию:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Реализация `IViewLocalizer` по умолчанию находит файл ресурсов по имени файла представления.

| Возможности использовать глобальный общий файл ресурсов нет. | `ViewLocalizer` реализует средство локализации с помощью интерфейса `IHtmlLocalizer`, поэтому Razor не кодирует локализованную строку в HTML. |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Вы можете параметризовать строки ресурсов, и `IViewLocalizer` будет кодировать в HTML параметры, но не строки ресурсов.

Рассмотрим следующую разметку Razor:

Файл ресурсов на французском языке может содержать следующие ресурсы:

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Ключ

Значение Преобразованное для просмотра представление будет содержать разметку HTML из файла ресурсов.

* **Примечание.** Как правило, требуется локализовать только текст, но не код HTML.
* Чтобы использовать в представлении общий файл ресурсов, внедрите `IHtmlLocalizer<T>`:

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

Локализация DataAnnotations Сообщения об ошибках DataAnnotations локализуются с помощью `IStringLocalizer<T>`.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>При использовании параметра `ResourcesPath = "Resources"` сообщения об ошибках в `RegisterViewModel` могут сохраняться по одному из следующих путей:

*Resources/ViewModels.Account.RegisterViewModel.fr.resx*

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

*Resources/ViewModels/Account/RegisterViewModel.fr.resx* В ASP.NET Core MVC 1.1.0 и более поздних версиях атрибуты, не относящиеся в проверке, локализуются.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>В ASP.NET Core MVC 1.0 **не** производится поиск локализованных строк для атрибутов, не относящихся к проверке.

### <a name="supportedcultures-and-supporteduicultures"></a>Использование одной строки ресурса для нескольких классов

В следующем коде показано, как использовать одну строку ресурса для атрибутов проверки с несколькими классами: В предыдущем коде `SharedResource` — это класс, соответствующий файлу RESX, в котором хранятся сообщения о проверке. При таком подходе DataAnnotations будут использовать только `SharedResource`, а не ресурс для каждого класса. Предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров SupportedCultures и SupportedUICultures ASP.NET Core позволяет указывать два значения языка и региональных параметров: `SupportedCultures` и `SupportedUICultures`. Объект [CultureInfo](/dotnet/api/system.globalization.cultureinfo) для `SupportedCultures` определяет результаты функций, зависящих от языка и региональных параметров: форматирование дат, времени, чисел, денежных единиц и т. д. `SupportedCultures` также определяет порядок сортировки текста, соглашения о регистре символов и способы сравнения строк. Дополнительные сведения о получении сервером значения языка и региональных параметров см. в описании свойства [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).

## <a name="resource-files"></a>Значение `SupportedUICultures` определяет то, какие строки переводов (в файле *RESX*) ищет объект [ResourceManager](/dotnet/api/system.resources.resourcemanager).

`ResourceManager` ищет связанные с языком и региональными параметрами строки, которые определяются значением `CurrentUICulture`. Каждый поток в .NET имеет объекты `CurrentCulture` и `CurrentUICulture`. ASP.NET Core проверяет эти значения при обработке функций, зависящих от языка и региональных параметров. Например, если для текущего потока заданы язык и региональные параметры "en-US" (английский, США), метод `DateTime.Now.ToLongDateString()` выводит строку "Thursday, February 18, 2016", но если `CurrentCulture` имеет значение "ru-RU" (русский, Россия), выводится строка "четверг, 18 февраля 2016 г." Файлы ресурсов

1. Файл ресурсов — это полезное средство для отделения локализуемых строк от кода.

    ![Переведенные строки на языках, отличных от языка по умолчанию, содержатся в отдельных файлах ресурсов с расширением *RESX*. Например, вам нужно создать файл ресурсов для испанского языка с именем *Welcome.es.resx*, который будет содержать переведенные строки.](localization/_static/newi.png)

2. "es" — это код испанского языка.

    ![Чтобы создать этот файл ресурсов в Visual Studio, выполните указанные ниже действия.](localization/_static/res.png)

3. В **обозревателе решений** щелкните правой кнопкой мыши папку, которая будет содержать файл ресурсов, а затем выберите пункты **Добавить** > **Новый элемент**.

    ![Вложенное контекстное меню: в обозревателе решений контекстное меню открывается для папки ресурсов.](localization/_static/hola.png)

    Второе контекстное меню с выделенными пунктами "Добавить" и "Новый элемент".

    ![В поле **Поиск установленных шаблонов** введите слово "ресурс" и укажите имя файла.](localization/_static/se.png)

## <a name="resource-file-naming"></a>Диалоговое окно ''Добавление нового элемента''

Введите значение ключа (строку на исходном языке) в столбце **Имя** и переведенную строку в столбце **Значение**. Файл Welcome.es.resx (файлов ресурсов с приветствиями на испанском языке) со словом "Hello" в столбце "Имя" и словом "Hol"a ("Hello" на испанском) в столбце "Значение" В Visual Studio отобразится файл *Welcome.es.resx*. Обозреватель решений с файлом ресурсов на испанском языке Именование файлов ресурсов

Имена ресурсов представляют собой полные имена типов соответствующего класса за исключением имени сборки. Например, ресурс на французском языке в проекте, главная сборка которого имеет имя `LocalizationWebsite.Web.dll`, для класса `LocalizationWebsite.Web.Startup` будет иметь имя *Startup.fr.resx*. Ресурс для класса `LocalizationWebsite.Web.Controllers.HomeController` будет иметь имя *Controllers.HomeController.fr.resx*. Если пространство имен целевого класса не совпадает с именем сборки, необходимо использовать полное имя типа. Например, в образце проекта ресурс для типа `ExtraNamespace.Tools` будет иметь имя *ExtraNamespace.Tools.fr.resx*. В образце проекта метод `ConfigureServices` присваивает свойству `ResourcesPath` значение "Resources", поэтому относительный путь к файлу ресурсов на французском языке для контроллера домашней страницы в проекте будет иметь вид *Resources/Controllers.HomeController.fr.resx*.

| Кроме того, для упорядочения файлов ресурсов можно использовать папки. | Для контроллера домашней страницы путь будет иметь вид *Resources/Controllers/HomeController.fr.resx*. |
| ------------   | ------------- |
| Если параметр `ResourcesPath` не используется, файл *RESX* будет находиться в базовом каталоге проекта. | Файл ресурса для `HomeController` будет иметь имя *Controllers.HomeController.fr.resx*.  |
| Выбор соглашения об именовании на основе точечной нотации или пути зависит от того, как следует упорядочивать файлы ресурсов.  | Имя ресурса |
|    |     |

Точечная нотация или путь Resources/Controllers.HomeController.fr.resx Точки Resources/Controllers/HomeController.fr.resx

* Path

* Файлы ресурсов, для которых используется директива `@inject IViewLocalizer` в представлениях Razor, следуют той же модели.

Файлу ресурсов для представления может присваиваться имя на основе либо точечной нотации, либо пути.

### <a name="rootnamespaceattribute"></a>В файле ресурсов для представления Razor имитируется путь к связанному файлу представления. 

Допустим, свойству `ResourcesPath` присвоено значение "Resources". В этом случае файл ресурсов на французском языке, связанный с представлением *Views/Home/About.cshtml*, может иметь одно из следующих имен: 

> [!WARNING]
> Resources/Views/Home/About.fr.resx Resources/Views.Home.About.fr.resx 

Если параметр `ResourcesPath` не используется, файл *RESX* для представления будет находиться в той же папке, что и представление.

* RootNamespaceAttribute
* Атрибут [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) содержит корневое пространство имен сборки, если корневое пространство имен сборки отличается от имени сборки. Это может произойти, если имя проекта — недопустимый идентификатор .NET. 

Например, `my-project-name.csproj` будет использовать корневое пространство имен `my_project_name` и имя сборки `my-project-name`, что повлечет эту ошибку.

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Если корневое пространство имен сборки отличается от имени сборки

## <a name="culture-fallback-behavior"></a>Локализация не работает по умолчанию.

Локализация завершается сбоем из-за метода поиска ресурсов в сборке. `RootNamespace` — это значение во время сборки, которое недоступно выполняющемуся процессу. Если `RootNamespace` отличается от `AssemblyName`, включите следующее в файл *AssemblyInfo.cs* (со значениями параметров, замененными фактическими значениями). Приведенный выше код обеспечивает успешное разрешение RESX-файлов. Резервный язык и региональные параметры При поиске ресурса локализации использует резервный язык и региональные параметры.

Если запрошенный язык и региональные параметры не найдены, используется родительский язык и региональные параметры. Кстати, родительский язык и региональные параметры представляют свойство [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent).

* Обычно (но не всегда) это означает удаление национального символа из ISO.
* Например, диалект испанского в Мексике — es-MX.
* Родительский элемент "es" (испанский) не относится к той или иной стране.

Допустим, сайт получает запрос на ресурс "Приветствие" с использованием языка и региональных параметров fr-CA. Система локализации ищет в следующих ресурсах по порядку и выбирает первое совпадение: *Welcome.fr-CA.resx*

### <a name="generate-resource-files-with-visual-studio"></a>*Welcome.fr.resx*

*Welcome.resx* (если `NeutralResourcesLanguage` — fr-CA) Если вы удалите указатель языка и региональных параметров ".fr" и при этом задан французский язык и региональные параметры, будет считан файл ресурсов по умолчанию и строки локализуются. Диспетчер ресурсов назначает ресурс по умолчанию или резервный ресурс на тот случай, когда соответствия запрошенному языку и региональным параметрам не найдено. Если нужно, чтобы при отсутствии ресурса для запрошенного языка и региональных параметров просто возвращался ключ, не используйте файл ресурсов по умолчанию. Создание файлов ресурсов с помощью Visual Studio

### <a name="add-other-cultures"></a>Если вы создаете в Visual Studio файл ресурсов, в имени которого не указаны язык и региональные параметры (например, *Welcome.resx*), Visual Studio создаст класс C# со свойством для каждой строки.

В ASP.NET Core обычно требуется иное поведение. Как правило, файл ресурсов *RESX* по умолчанию (файл *RESX* без указания языка и региональных параметров) не используется. Мы рекомендуем создать файл *RESX* с указанием языка и региональных параметров (например, *Welcome.fr.resx*).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>При создании файла *RESX* с указанием языка и региональных параметров среда Visual Studio не создает файл класса.

### <a name="configure-localization"></a>Добавление других языков и региональных параметров

Каждое сочетание языка и региональных параметров (кроме языка по умолчанию) требует уникального файла ресурсов.

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* Создавая файлы ресурсов для разных языков, языковых стандартов и региональных параметров, вы включаете в их имена коды языков ISO (например, **en-us**, **ru-ru** и **fr-ca**). Эти коды ISO помещаются между именем файла и расширением *RESX*, например *Welcome.es-MX.resx* (испанский, Мексика).

* реализацию стратегии по выбору языка и региональных параметров для каждого запроса. Настройка локализации Локализация настраивается в методе `Startup.ConfigureServices`:

* Метод `AddLocalization` добавляет службы локализации в контейнер служб.

### <a name="localization-middleware"></a>В приведенном выше коде также задается путь к ресурсам "Resources".

Метод `AddViewLocalization` добавляет поддержку файлов локализованных представлений. В этом примере локализация образца представления основана на суффиксе файла представления, например "fr" в файле *Index.fr.cshtml*.

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Метод `AddDataAnnotationsLocalization` добавляет поддержку локализованных сообщений проверки `DataAnnotations` посредством абстракций `IStringLocalizer`. Промежуточный слой локализации Текущий язык и региональные параметры в запросе задаются в [промежуточном слое](xref:fundamentals/middleware/index) локализации.

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

Промежуточный слой локализации включается в методе `Startup.Configure`. Промежуточный слой локализации должен настраиваться перед другими промежуточными слоями, которые могут проверять язык и региональные параметры запроса (например, `app.UseMvcWithDefaultRoute()`). `UseRequestLocalization` инициализирует объект `RequestLocalizationOptions`.

### <a name="querystringrequestcultureprovider"></a>При каждом запросе проверяется список поставщиков `RequestCultureProvider` в объекте `RequestLocalizationOptions` и используется первый поставщик, который может успешно определить язык и региональные параметры запроса.

Поставщики по умолчанию берутся из класса `RequestLocalizationOptions`: В списке по умолчанию поставщики следуют от наиболее конкретных до наиболее общих. Далее в этой статье вы узнаете, как можно изменить этот порядок и даже добавить пользовательский поставщик языка и региональных параметров. Если ни один из поставщиков не может определить язык и региональные параметры запроса, используется `DefaultRequestCulture`. QueryStringRequestCultureProvider

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Некоторые приложения используют строку запроса для указания [языка и региональных параметров самого приложения и пользовательского интерфейса](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx). Если в приложении для этого применяется файл cookie или заголовок Accept-Language, добавление строки запроса к URL-адресу может быть полезным в целях отладки и тестирования кода.

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>По умолчанию поставщик `QueryStringRequestCultureProvider` регистрируется в качестве первого поставщика локализации в списке `RequestCultureProvider`.

Вы передаете параметры строки запроса `culture` и `ui-culture`. В следующем примере в качестве языка и региональных параметров (язык и регион) задаются испанский язык и Мексика:

Если передан только один из двух параметров (`culture` или `ui-culture`), поставщик строки запроса задаст с его помощью оба значения. Например, если передан только язык и региональные параметры, будут заданы оба значения `Culture` и `UICulture`:

CookieRequestCultureProvider

    c=en-UK|uic=en-US

Рабочие приложения часто предоставляют механизм для указания языка и региональных параметров с помощью соответствующего файла cookie ASP.NET Core.

### <a name="the-accept-language-http-header"></a>Чтобы создать файл cookie, используйте метод `MakeCookieValue`.

`CookieRequestCultureProvider` `DefaultCookieName` возвращает имя файла cookie по умолчанию, с помощью которого отслеживается предпочтительный язык и региональные параметры пользователя. Имя файла cookie по умолчанию — `.AspNetCore.Culture`. Файл cookie имеет формат `c=%LANGCODE%|uic=%LANGCODE%`, где `c` — это `Culture`, а `uic` — это `UICulture`, например: Если указаны либо только язык и региональные параметры, либо только язык и региональные параметры пользовательского интерфейса, это значение будет использоваться для обоих параметров.

### <a name="set-the-accept-language-http-header-in-ie"></a>Заголовок HTTP Accept-Language

1. [Заголовок Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) может задаваться в большинстве браузеров и изначально предназначался для указания языка пользователя.

2. Он показывает, какой язык был выбран в браузере или унаследован от базовой операционной системы.

    ![Заголовок HTTP Accept-Language в запросе из браузера — это не самый надежный способ определения предпочтительного языка пользователя (см. статью [Настройка языковых предпочтений в браузере](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).](localization/_static/lang.png)

3. В рабочем приложении должна быть возможность выбора языка и региональных параметров пользователем.

4. Задание заголовка HTTP Accept-Language в Internet Explorer

5. Щелкните значок шестеренки и выберите пункт **Свойства браузера**.

6. Нажмите кнопку **Языки**.

### <a name="use-a-custom-provider"></a>Свойства браузера

Нажмите **Задать предпочитаемые языки**. Нажмите **Добавить язык**. Добавьте язык.

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

Выберите язык, а затем нажмите кнопку **Вверх**.

### <a name="set-the-culture-programmatically"></a>Использование пользовательского поставщика

Предположим, необходимо, чтобы ваши клиенты могли сохранять информацию о своем языке и региональных параметрах в ваших базах данных. Можно написать поставщик, который будет искать эти значения для пользователя.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

В следующем примере кода показано, как добавить пользовательский поставщик:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

Для добавления или удаления поставщиков локализации используйте объект `RequestLocalizationOptions`.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Задание языка и региональных параметров программным образом В образце проекта **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть пользовательский интерфейс для задания значения `Culture`.

## <a name="model-binding-route-data-and-query-strings"></a>Файл *Views/Shared/_SelectLanguagePartial.cshtml* позволяет выбрать язык и региональные параметры из списка поддерживаемых:

Файл *Views/Shared/_SelectLanguagePartial.cshtml* добавляется в раздел `footer` файла макета, поэтому он доступен всем представлениям:

## <a name="globalization-and-localization-terms"></a>Метод `SetLanguage` задает файл cookie языка и региональных параметров.

Подключить файл *_SelectLanguagePartial.cshtml* к образцу кода этого проекта нельзя. В проекте **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть код для передачи объекта `RequestLocalizationOptions` в частичное представление Razor посредством контейнера [внедрения зависимостей](dependency-injection.md). Данные маршрутов привязки модели и строки запросов

См. раздел [Поведение глобализации для данных маршрутов привязки модели и строк запросов](xref:mvc/models/model-binding#glob).

Термины, относящиеся к глобализации и локализации Процесс локализации приложения требует базового понимания распространенных кодировок, часто используемых при разработке современного программного обеспечения, и связанных с ними проблем. Хотя на всех компьютерах текст сохраняется в виде цифр (кодов), в разных системах эти коды для одного и того же текста различаются. Под процессом локализации понимается перевод пользовательского интерфейса приложения для определенного языка и региональных параметров.

[Возможность локализации](/dotnet/standard/globalization-localization/localizability-review) — это определяемое на промежуточной стадии состояние готовности глобализованного приложения к локализации. Формат [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) для названия языка и региональных параметров имеет вид `<languagecode2>-<country/regioncode2>`, где `<languagecode2>` — это код языка, а `<country/regioncode2>` — код субкультуры. Примеры: `es-CL` для испанского языка (Чили), `en-US` для английского языка (США), `en-AU` для английского языка (Австралия).

[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) — это комбинация двухбуквенного кода культуры ISO 639 в нижнем регистре (он связан с языком) и двухбуквенного кода субкультуры ISO 3166 в верхнем регистре (он связан со страной или регионом).

* См. статью [Имя языка и региональных параметров](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).
* Вместо слова "интернационализация" (internationalization) часто используют аббревиатуру "I18N".
* В аббревиатуру включаются первая и последняя буквы слова, а также число букв между ними, то есть 18 — это число букв между первой ("I") и последней ("N") буквами.
* То же самое касается глобализации (Globalization — G11N) и локализации (Localization — L10N).
* Термины: Глобализация (G11N): процесс подготовки приложения к поддержке различных языков и регионов.
* Локализация (L10N): процесс настройки приложения для работы с конкретным языком и регионом. Интернационализация (I18N): описывает глобализацию и локализацию.
* Язык и региональные параметры: обозначает язык и (необязательно) регион. Нейтральный язык и региональные параметры: язык и региональные параметры, для которых указан язык, но не регион.
* (например, "en", "es").

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>Конкретный язык и региональные параметры: язык и региональные параметры, для которых указаны язык и регион.

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* (например, "en-US", "en-GB", "es-CL").
* Родительский язык и региональные параметры: нейтральные язык и региональные параметры, содержащие конкретные язык и региональные параметры.
* (например, "en" — это родительские язык и региональные параметры для "en-US" и "en-GB").
* Языковой стандарт: тот же, что и язык и региональные параметры.
* Дополнительные ресурсы

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Проект Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb), используемый в этой статье.

[Глобализация и локализация приложений .NET](/dotnet/standard/globalization-localization/index) [Ресурсы в RESX-файлах](/dotnet/framework/resources/working-with-resx-files-programmatically)

[Набор средств многоязычных приложений Майкрософт](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308) [Локализация и универсальные шаблоны](http://hishambinateya.com/localization-and-generics) Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Дэмиен Боуден](https://twitter.com/damien_bod) (Damien Bowden), [Барт Каликсто](https://twitter.com/bartmax) (Bart Calixto), [Надим Афана](https://afana.me/) (Nadeem Afana) и [Хишам Бин Атея](https://twitter.com/hishambinateya) (Hisham Bin Ateya)

Создание многоязычного веб-сайта позволит расширить аудиторию. ASP.NET Core предоставляет службы и ПО промежуточного слоя для локализации на разные языки и для разных региональных параметров.

Интернационализация предполагает [глобализацию](/dotnet/api/system.globalization) и [локализацию](/dotnet/standard/globalization-localization/localization).

1. Глобализация — это процесс разработки приложений, которые поддерживают разные языки и региональные параметры.
1. Глобализация добавляет поддержку ввода отображения и вывода определенного набора языковых сценариев, относящихся к конкретным регионам.
1. Локализация — это процесс адаптации глобализованного приложения, уже подготовленного к локализации, к определенному языку, языковому стандарту и региональным параметрам.

Дополнительные сведения см. в разделе **Термины, относящиеся к глобализации и локализации** ближе к концу этого документа.

## <a name="make-the-apps-content-localizable"></a>Локализация приложения включает следующие задачи:

обеспечение возможности локализации для содержимого приложения; предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров; реализацию стратегии по выбору языка и региональных параметров для каждого запроса. [Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([как скачивать](xref:index#how-to-download-a-sample)) Обеспечение возможности локализации для содержимого приложения <xref:Microsoft.Extensions.Localization.IStringLocalizer> и <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> создавались для повышения производительность при разработке локализованных приложений.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

`IStringLocalizer` использует классы <xref:System.Resources.ResourceManager> и <xref:System.Resources.ResourceReader> для предоставления ресурсов, связанных с определенным языком и региональными параметрами, во время выполнения. Этот интерфейс имеет индексатор и интерфейс `IEnumerable` для возврата локализованных строк. `IStringLocalizer` не требует сохранять строки на языке по умолчанию в файле ресурсов. Вы можете разрабатывать приложение, предназначенное для локализации, не создавая файлы ресурсов на ранних этапах разработки. В приведенном ниже коде показано, как подготовить строку "About Title" для локализации. В предыдущем коде реализация `IStringLocalizer<T>` получена в результате [внедрения зависимостей](dependency-injection.md). Если локализованное значение для строки "About Title" не найдено, возвращается ключ индексатора, то есть строка "About Title".

Вы можете оставить литеральные строки на языке по умолчанию и заключить их в средство локализации, чтобы сосредоточиться на разработке приложения. Вы разрабатываете приложение на языке по умолчанию и подготавливаете его к локализации, не создавая предварительно файл ресурсов по умолчанию. Вы также можете выбрать традиционный подход и предоставить ключ для извлечения строки на языке по умолчанию.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

Для многих разработчиков новый рабочий процесс, который не требует наличия файла *RESX* на языке по умолчанию и позволяет просто заключать строки для дальнейшей обработки, может помочь снизить затраты на локализацию приложения.

Другие разработчики могут предпочесть традиционный рабочий процесс, так как он упрощает работу с более длинными строками и обновление локализованных строк.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Используйте реализацию `IHtmlLocalizer<T>` для ресурсов, содержащих код HTML.

Интерфейс `IHtmlLocalizer` кодирует в HTML аргументы, отформатированные в строке ресурса, но не кодирует в HTML саму строку. В выделенной ниже строке в HTML кодируется только значение параметра `name`.

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

**Примечание.** Как правило, требуется локализовать только текст, но не код HTML. На самом нижнем уровне интерфейс `IStringLocalizerFactory` можно получить из [внедрения зависимостей](dependency-injection.md):

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>В приведенном выше коде демонстрируются оба фабричных метода создания.

Вы можете разбивать локализованные строки по контроллеру, области или использовать всего один конвейер. В образце приложения для общих ресурсов применяется класс-заглушка с именем `SharedResource`. Некоторые разработчики используют класс `Startup` для хранения глобальных или общих строк.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

В примере ниже используются средства локализации `InfoController` и `SharedResource`. Локализация представления Служба `IViewLocalizer` предоставляет локализованные строки для [представления](xref:mvc/views/overview). Класс `ViewLocalizer` реализует этот интерфейс и находит расположение ресурсов по пути к файлу представления. В следующем примере кода демонстрируется использование реализации `IViewLocalizer` по умолчанию:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Реализация `IViewLocalizer` по умолчанию находит файл ресурсов по имени файла представления.

| Возможности использовать глобальный общий файл ресурсов нет. | `ViewLocalizer` реализует средство локализации с помощью интерфейса `IHtmlLocalizer`, поэтому Razor не кодирует локализованную строку в HTML. |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Вы можете параметризовать строки ресурсов, и `IViewLocalizer` будет кодировать в HTML параметры, но не строки ресурсов.

Рассмотрим следующую разметку Razor:

Файл ресурсов на французском языке может содержать следующие ресурсы:

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Ключ

Значение Преобразованное для просмотра представление будет содержать разметку HTML из файла ресурсов.

* **Примечание.** Как правило, требуется локализовать только текст, но не код HTML.
* Чтобы использовать в представлении общий файл ресурсов, внедрите `IHtmlLocalizer<T>`:

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

Локализация DataAnnotations Сообщения об ошибках DataAnnotations локализуются с помощью `IStringLocalizer<T>`.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>При использовании параметра `ResourcesPath = "Resources"` сообщения об ошибках в `RegisterViewModel` могут сохраняться по одному из следующих путей:

*Resources/ViewModels.Account.RegisterViewModel.fr.resx*

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

*Resources/ViewModels/Account/RegisterViewModel.fr.resx* В ASP.NET Core MVC 1.1.0 и более поздних версиях атрибуты, не относящиеся в проверке, локализуются.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>В ASP.NET Core MVC 1.0 **не** производится поиск локализованных строк для атрибутов, не относящихся к проверке.

### <a name="supportedcultures-and-supporteduicultures"></a>Использование одной строки ресурса для нескольких классов

В следующем коде показано, как использовать одну строку ресурса для атрибутов проверки с несколькими классами: В предыдущем коде `SharedResource` — это класс, соответствующий файлу RESX, в котором хранятся сообщения о проверке. При таком подходе DataAnnotations будут использовать только `SharedResource`, а не ресурс для каждого класса. Предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров SupportedCultures и SupportedUICultures ASP.NET Core позволяет указывать два значения языка и региональных параметров: `SupportedCultures` и `SupportedUICultures`. Объект [CultureInfo](/dotnet/api/system.globalization.cultureinfo) для `SupportedCultures` определяет результаты функций, зависящих от языка и региональных параметров: форматирование дат, времени, чисел, денежных единиц и т. д. `SupportedCultures` также определяет порядок сортировки текста, соглашения о регистре символов и способы сравнения строк. Дополнительные сведения о получении сервером значения языка и региональных параметров см. в описании свойства [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).

## <a name="resource-files"></a>Значение `SupportedUICultures` определяет то, какие строки переводов (в файле *RESX*) ищет объект [ResourceManager](/dotnet/api/system.resources.resourcemanager).

`ResourceManager` ищет связанные с языком и региональными параметрами строки, которые определяются значением `CurrentUICulture`. Каждый поток в .NET имеет объекты `CurrentCulture` и `CurrentUICulture`. ASP.NET Core проверяет эти значения при обработке функций, зависящих от языка и региональных параметров. Например, если для текущего потока заданы язык и региональные параметры "en-US" (английский, США), метод `DateTime.Now.ToLongDateString()` выводит строку "Thursday, February 18, 2016", но если `CurrentCulture` имеет значение "ru-RU" (русский, Россия), выводится строка "четверг, 18 февраля 2016 г." Файлы ресурсов

1. Файл ресурсов — это полезное средство для отделения локализуемых строк от кода.

    ![Переведенные строки на языках, отличных от языка по умолчанию, содержатся в отдельных файлах ресурсов с расширением *RESX*. Например, вам нужно создать файл ресурсов для испанского языка с именем *Welcome.es.resx*, который будет содержать переведенные строки.](localization/_static/newi.png)

2. "es" — это код испанского языка.

    ![Чтобы создать этот файл ресурсов в Visual Studio, выполните указанные ниже действия.](localization/_static/res.png)

3. В **обозревателе решений** щелкните правой кнопкой мыши папку, которая будет содержать файл ресурсов, а затем выберите пункты **Добавить** > **Новый элемент**.

    ![Вложенное контекстное меню: в обозревателе решений контекстное меню открывается для папки ресурсов.](localization/_static/hola.png)

    Второе контекстное меню с выделенными пунктами "Добавить" и "Новый элемент".

    ![В поле **Поиск установленных шаблонов** введите слово "ресурс" и укажите имя файла.](localization/_static/se.png)

## <a name="resource-file-naming"></a>Диалоговое окно ''Добавление нового элемента''

Введите значение ключа (строку на исходном языке) в столбце **Имя** и переведенную строку в столбце **Значение**. Файл Welcome.es.resx (файлов ресурсов с приветствиями на испанском языке) со словом "Hello" в столбце "Имя" и словом "Hol"a ("Hello" на испанском) в столбце "Значение" В Visual Studio отобразится файл *Welcome.es.resx*. Обозреватель решений с файлом ресурсов на испанском языке Именование файлов ресурсов

Имена ресурсов представляют собой полные имена типов соответствующего класса за исключением имени сборки. Например, ресурс на французском языке в проекте, главная сборка которого имеет имя `LocalizationWebsite.Web.dll`, для класса `LocalizationWebsite.Web.Startup` будет иметь имя *Startup.fr.resx*. Ресурс для класса `LocalizationWebsite.Web.Controllers.HomeController` будет иметь имя *Controllers.HomeController.fr.resx*. Если пространство имен целевого класса не совпадает с именем сборки, необходимо использовать полное имя типа. Например, в образце проекта ресурс для типа `ExtraNamespace.Tools` будет иметь имя *ExtraNamespace.Tools.fr.resx*. В образце проекта метод `ConfigureServices` присваивает свойству `ResourcesPath` значение "Resources", поэтому относительный путь к файлу ресурсов на французском языке для контроллера домашней страницы в проекте будет иметь вид *Resources/Controllers.HomeController.fr.resx*.

| Кроме того, для упорядочения файлов ресурсов можно использовать папки. | Для контроллера домашней страницы путь будет иметь вид *Resources/Controllers/HomeController.fr.resx*. |
| ------------   | ------------- |
| Если параметр `ResourcesPath` не используется, файл *RESX* будет находиться в базовом каталоге проекта. | Файл ресурса для `HomeController` будет иметь имя *Controllers.HomeController.fr.resx*.  |
| Выбор соглашения об именовании на основе точечной нотации или пути зависит от того, как следует упорядочивать файлы ресурсов.  | Имя ресурса |
|    |     |

Точечная нотация или путь Resources/Controllers.HomeController.fr.resx Точки Resources/Controllers/HomeController.fr.resx

* Path

* Файлы ресурсов, для которых используется директива `@inject IViewLocalizer` в представлениях Razor, следуют той же модели.

Файлу ресурсов для представления может присваиваться имя на основе либо точечной нотации, либо пути.

### <a name="rootnamespaceattribute"></a>В файле ресурсов для представления Razor имитируется путь к связанному файлу представления. 

Допустим, свойству `ResourcesPath` присвоено значение "Resources". В этом случае файл ресурсов на французском языке, связанный с представлением *Views/Home/About.cshtml*, может иметь одно из следующих имен: 

> [!WARNING]
> Resources/Views/Home/About.fr.resx Resources/Views.Home.About.fr.resx 

Если параметр `ResourcesPath` не используется, файл *RESX* для представления будет находиться в той же папке, что и представление.

* RootNamespaceAttribute
* Атрибут [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) содержит корневое пространство имен сборки, если корневое пространство имен сборки отличается от имени сборки. Это может произойти, если имя проекта — недопустимый идентификатор .NET. 

Например, `my-project-name.csproj` будет использовать корневое пространство имен `my_project_name` и имя сборки `my-project-name`, что повлечет эту ошибку.

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Если корневое пространство имен сборки отличается от имени сборки

## <a name="culture-fallback-behavior"></a>Локализация не работает по умолчанию.

Локализация завершается сбоем из-за метода поиска ресурсов в сборке. `RootNamespace` — это значение во время сборки, которое недоступно выполняющемуся процессу. Если `RootNamespace` отличается от `AssemblyName`, включите следующее в файл *AssemblyInfo.cs* (со значениями параметров, замененными фактическими значениями). Приведенный выше код обеспечивает успешное разрешение RESX-файлов. Резервный язык и региональные параметры При поиске ресурса локализации использует резервный язык и региональные параметры.

Если запрошенный язык и региональные параметры не найдены, используется родительский язык и региональные параметры. Кстати, родительский язык и региональные параметры представляют свойство [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent).

* Обычно (но не всегда) это означает удаление национального символа из ISO.
* Например, диалект испанского в Мексике — es-MX.
* Родительский элемент "es" (испанский) не относится к той или иной стране.

Допустим, сайт получает запрос на ресурс "Приветствие" с использованием языка и региональных параметров fr-CA. Система локализации ищет в следующих ресурсах по порядку и выбирает первое совпадение: *Welcome.fr-CA.resx*

### <a name="generate-resource-files-with-visual-studio"></a>*Welcome.fr.resx*

*Welcome.resx* (если `NeutralResourcesLanguage` — fr-CA) Если вы удалите указатель языка и региональных параметров ".fr" и при этом задан французский язык и региональные параметры, будет считан файл ресурсов по умолчанию и строки локализуются. Диспетчер ресурсов назначает ресурс по умолчанию или резервный ресурс на тот случай, когда соответствия запрошенному языку и региональным параметрам не найдено. Если нужно, чтобы при отсутствии ресурса для запрошенного языка и региональных параметров просто возвращался ключ, не используйте файл ресурсов по умолчанию. Создание файлов ресурсов с помощью Visual Studio

### <a name="add-other-cultures"></a>Если вы создаете в Visual Studio файл ресурсов, в имени которого не указаны язык и региональные параметры (например, *Welcome.resx*), Visual Studio создаст класс C# со свойством для каждой строки.

В ASP.NET Core обычно требуется иное поведение. Как правило, файл ресурсов *RESX* по умолчанию (файл *RESX* без указания языка и региональных параметров) не используется. Мы рекомендуем создать файл *RESX* с указанием языка и региональных параметров (например, *Welcome.fr.resx*).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>При создании файла *RESX* с указанием языка и региональных параметров среда Visual Studio не создает файл класса.

### <a name="configure-localization"></a>Добавление других языков и региональных параметров

Каждое сочетание языка и региональных параметров (кроме языка по умолчанию) требует уникального файла ресурсов.

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* Создавая файлы ресурсов для разных языков, языковых стандартов и региональных параметров, вы включаете в их имена коды языков ISO (например, **en-us**, **ru-ru** и **fr-ca**). Эти коды ISO помещаются между именем файла и расширением *RESX*, например *Welcome.es-MX.resx* (испанский, Мексика).

* реализацию стратегии по выбору языка и региональных параметров для каждого запроса. Настройка локализации Локализация настраивается в методе `Startup.ConfigureServices`:

* Метод `AddLocalization` добавляет службы локализации в контейнер служб.

### <a name="localization-middleware"></a>В приведенном выше коде также задается путь к ресурсам "Resources".

Метод `AddViewLocalization` добавляет поддержку файлов локализованных представлений. В этом примере локализация образца представления основана на суффиксе файла представления, например "fr" в файле *Index.fr.cshtml*.

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Метод `AddDataAnnotationsLocalization` добавляет поддержку локализованных сообщений проверки `DataAnnotations` посредством абстракций `IStringLocalizer`. Промежуточный слой локализации Текущий язык и региональные параметры в запросе задаются в [промежуточном слое](xref:fundamentals/middleware/index) локализации.

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

Промежуточный слой локализации включается в методе `Startup.Configure`. Промежуточный слой локализации должен настраиваться перед другими промежуточными слоями, которые могут проверять язык и региональные параметры запроса (например, `app.UseMvcWithDefaultRoute()`). `UseRequestLocalization` инициализирует объект `RequestLocalizationOptions`.

### <a name="querystringrequestcultureprovider"></a>При каждом запросе проверяется список поставщиков `RequestCultureProvider` в объекте `RequestLocalizationOptions` и используется первый поставщик, который может успешно определить язык и региональные параметры запроса.

Поставщики по умолчанию берутся из класса `RequestLocalizationOptions`: В списке по умолчанию поставщики следуют от наиболее конкретных до наиболее общих. Далее в этой статье вы узнаете, как можно изменить этот порядок и даже добавить пользовательский поставщик языка и региональных параметров. Если ни один из поставщиков не может определить язык и региональные параметры запроса, используется `DefaultRequestCulture`. QueryStringRequestCultureProvider

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Некоторые приложения используют строку запроса для указания [языка и региональных параметров самого приложения и пользовательского интерфейса](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx). Если в приложении для этого применяется файл cookie или заголовок Accept-Language, добавление строки запроса к URL-адресу может быть полезным в целях отладки и тестирования кода.

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>По умолчанию поставщик `QueryStringRequestCultureProvider` регистрируется в качестве первого поставщика локализации в списке `RequestCultureProvider`.

Вы передаете параметры строки запроса `culture` и `ui-culture`. В следующем примере в качестве языка и региональных параметров (язык и регион) задаются испанский язык и Мексика:

Если передан только один из двух параметров (`culture` или `ui-culture`), поставщик строки запроса задаст с его помощью оба значения. Например, если передан только язык и региональные параметры, будут заданы оба значения `Culture` и `UICulture`:

CookieRequestCultureProvider

    c=en-UK|uic=en-US

Рабочие приложения часто предоставляют механизм для указания языка и региональных параметров с помощью соответствующего файла cookie ASP.NET Core.

### <a name="the-accept-language-http-header"></a>Чтобы создать файл cookie, используйте метод `MakeCookieValue`.

`CookieRequestCultureProvider` `DefaultCookieName` возвращает имя файла cookie по умолчанию, с помощью которого отслеживается предпочтительный язык и региональные параметры пользователя. Имя файла cookie по умолчанию — `.AspNetCore.Culture`. Файл cookie имеет формат `c=%LANGCODE%|uic=%LANGCODE%`, где `c` — это `Culture`, а `uic` — это `UICulture`, например: Если указаны либо только язык и региональные параметры, либо только язык и региональные параметры пользовательского интерфейса, это значение будет использоваться для обоих параметров.

### <a name="set-the-accept-language-http-header-in-ie"></a>Заголовок HTTP Accept-Language

1. [Заголовок Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) может задаваться в большинстве браузеров и изначально предназначался для указания языка пользователя.

2. Он показывает, какой язык был выбран в браузере или унаследован от базовой операционной системы.

    ![Заголовок HTTP Accept-Language в запросе из браузера — это не самый надежный способ определения предпочтительного языка пользователя (см. статью [Настройка языковых предпочтений в браузере](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).](localization/_static/lang.png)

3. В рабочем приложении должна быть возможность выбора языка и региональных параметров пользователем.

4. Задание заголовка HTTP Accept-Language в Internet Explorer

5. Щелкните значок шестеренки и выберите пункт **Свойства браузера**.

6. Нажмите кнопку **Языки**.

### <a name="use-a-custom-provider"></a>Свойства браузера

Нажмите **Задать предпочитаемые языки**. Нажмите **Добавить язык**. Добавьте язык.

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

Выберите язык, а затем нажмите кнопку **Вверх**.

### <a name="set-the-culture-programmatically"></a>Использование пользовательского поставщика

Предположим, необходимо, чтобы ваши клиенты могли сохранять информацию о своем языке и региональных параметрах в ваших базах данных. Можно написать поставщик, который будет искать эти значения для пользователя.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

В следующем примере кода показано, как добавить пользовательский поставщик:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

Для добавления или удаления поставщиков локализации используйте объект `RequestLocalizationOptions`.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Задание языка и региональных параметров программным образом В образце проекта **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть пользовательский интерфейс для задания значения `Culture`.

## <a name="model-binding-route-data-and-query-strings"></a>Файл *Views/Shared/_SelectLanguagePartial.cshtml* позволяет выбрать язык и региональные параметры из списка поддерживаемых:

Файл *Views/Shared/_SelectLanguagePartial.cshtml* добавляется в раздел `footer` файла макета, поэтому он доступен всем представлениям:

## <a name="globalization-and-localization-terms"></a>Метод `SetLanguage` задает файл cookie языка и региональных параметров.

Подключить файл *_SelectLanguagePartial.cshtml* к образцу кода этого проекта нельзя. В проекте **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть код для передачи объекта `RequestLocalizationOptions` в частичное представление Razor посредством контейнера [внедрения зависимостей](dependency-injection.md). Данные маршрутов привязки модели и строки запросов

См. раздел [Поведение глобализации для данных маршрутов привязки модели и строк запросов](xref:mvc/models/model-binding#glob).

Термины, относящиеся к глобализации и локализации Процесс локализации приложения требует базового понимания распространенных кодировок, часто используемых при разработке современного программного обеспечения, и связанных с ними проблем. Хотя на всех компьютерах текст сохраняется в виде цифр (кодов), в разных системах эти коды для одного и того же текста различаются. Под процессом локализации понимается перевод пользовательского интерфейса приложения для определенного языка и региональных параметров.

[Возможность локализации](/dotnet/standard/globalization-localization/localizability-review) — это определяемое на промежуточной стадии состояние готовности глобализованного приложения к локализации. Формат [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) для названия языка и региональных параметров имеет вид `<languagecode2>-<country/regioncode2>`, где `<languagecode2>` — это код языка, а `<country/regioncode2>` — код субкультуры. Примеры: `es-CL` для испанского языка (Чили), `en-US` для английского языка (США), `en-AU` для английского языка (Австралия).

[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) — это комбинация двухбуквенного кода культуры ISO 639 в нижнем регистре (он связан с языком) и двухбуквенного кода субкультуры ISO 3166 в верхнем регистре (он связан со страной или регионом).

* См. статью [Имя языка и региональных параметров](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).
* Вместо слова "интернационализация" (internationalization) часто используют аббревиатуру "I18N".
* В аббревиатуру включаются первая и последняя буквы слова, а также число букв между ними, то есть 18 — это число букв между первой ("I") и последней ("N") буквами.
* То же самое касается глобализации (Globalization — G11N) и локализации (Localization — L10N).
* Термины: Глобализация (G11N): процесс подготовки приложения к поддержке различных языков и регионов.
* Локализация (L10N): процесс настройки приложения для работы с конкретным языком и регионом. Интернационализация (I18N): описывает глобализацию и локализацию.
* Язык и региональные параметры: обозначает язык и (необязательно) регион. Нейтральный язык и региональные параметры: язык и региональные параметры, для которых указан язык, но не регион.
* (например, "en", "es").

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a>Конкретный язык и региональные параметры: язык и региональные параметры, для которых указаны язык и регион.

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* (например, "en-US", "en-GB", "es-CL").
* Родительский язык и региональные параметры: нейтральные язык и региональные параметры, содержащие конкретные язык и региональные параметры.
* (например, "en" — это родительские язык и региональные параметры для "en-US" и "en-GB").
* Языковой стандарт: тот же, что и язык и региональные параметры.
* Дополнительные ресурсы

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

[Проект Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb), используемый в этой статье.

[Глобализация и локализация приложений .NET](/dotnet/standard/globalization-localization/index) [Ресурсы в RESX-файлах](/dotnet/framework/resources/working-with-resx-files-programmatically)

[Набор средств многоязычных приложений Майкрософт](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308) [Локализация и универсальные шаблоны](http://hishambinateya.com/localization-and-generics) Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Дэмиен Боуден](https://twitter.com/damien_bod) (Damien Bowden), [Барт Каликсто](https://twitter.com/bartmax) (Bart Calixto), [Надим Афана](https://afana.me/) (Nadeem Afana) и [Хишам Бин Атея](https://twitter.com/hishambinateya) (Hisham Bin Ateya)

Создание многоязычного веб-сайта позволит расширить аудиторию. ASP.NET Core предоставляет службы и ПО промежуточного слоя для локализации на разные языки и для разных региональных параметров.

Интернационализация предполагает [глобализацию](/dotnet/api/system.globalization) и [локализацию](/dotnet/standard/globalization-localization/localization).

1. Глобализация — это процесс разработки приложений, которые поддерживают разные языки и региональные параметры.
1. Глобализация добавляет поддержку ввода отображения и вывода определенного набора языковых сценариев, относящихся к конкретным регионам.
1. Локализация — это процесс адаптации глобализованного приложения, уже подготовленного к локализации, к определенному языку, языковому стандарту и региональным параметрам.

Дополнительные сведения см. в разделе **Термины, относящиеся к глобализации и локализации** ближе к концу этого документа.

## <a name="make-the-apps-content-localizable"></a>Локализация приложения включает следующие задачи:

обеспечение возможности локализации для содержимого приложения; предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров; реализацию стратегии по выбору языка и региональных параметров для каждого запроса. [Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([как скачивать](xref:index#how-to-download-a-sample)) Обеспечение возможности локализации для содержимого приложения <xref:Microsoft.Extensions.Localization.IStringLocalizer> и <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> создавались для повышения производительность при разработке локализованных приложений.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

`IStringLocalizer` использует классы <xref:System.Resources.ResourceManager> и <xref:System.Resources.ResourceReader> для предоставления ресурсов, связанных с определенным языком и региональными параметрами, во время выполнения. Этот интерфейс имеет индексатор и интерфейс `IEnumerable` для возврата локализованных строк. `IStringLocalizer` не требует сохранять строки на языке по умолчанию в файле ресурсов. Вы можете разрабатывать приложение, предназначенное для локализации, не создавая файлы ресурсов на ранних этапах разработки. В приведенном ниже коде показано, как подготовить строку "About Title" для локализации. В предыдущем коде реализация `IStringLocalizer<T>` получена в результате [внедрения зависимостей](dependency-injection.md). Если локализованное значение для строки "About Title" не найдено, возвращается ключ индексатора, то есть строка "About Title".

Вы можете оставить литеральные строки на языке по умолчанию и заключить их в средство локализации, чтобы сосредоточиться на разработке приложения. Вы разрабатываете приложение на языке по умолчанию и подготавливаете его к локализации, не создавая предварительно файл ресурсов по умолчанию. Вы также можете выбрать традиционный подход и предоставить ключ для извлечения строки на языке по умолчанию.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

Для многих разработчиков новый рабочий процесс, который не требует наличия файла *RESX* на языке по умолчанию и позволяет просто заключать строки для дальнейшей обработки, может помочь снизить затраты на локализацию приложения.

Другие разработчики могут предпочесть традиционный рабочий процесс, так как он упрощает работу с более длинными строками и обновление локализованных строк.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Используйте реализацию `IHtmlLocalizer<T>` для ресурсов, содержащих код HTML.

Интерфейс `IHtmlLocalizer` кодирует в HTML аргументы, отформатированные в строке ресурса, но не кодирует в HTML саму строку. В выделенной ниже строке в HTML кодируется только значение параметра `name`.

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

**Примечание.** Как правило, требуется локализовать только текст, но не код HTML. На самом нижнем уровне интерфейс `IStringLocalizerFactory` можно получить из [внедрения зависимостей](dependency-injection.md):

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>В приведенном выше коде демонстрируются оба фабричных метода создания.

Вы можете разбивать локализованные строки по контроллеру, области или использовать всего один конвейер. В образце приложения для общих ресурсов применяется класс-заглушка с именем `SharedResource`. Некоторые разработчики используют класс `Startup` для хранения глобальных или общих строк.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

В примере ниже используются средства локализации `InfoController` и `SharedResource`. Локализация представления Служба `IViewLocalizer` предоставляет локализованные строки для [представления](xref:mvc/views/overview). Класс `ViewLocalizer` реализует этот интерфейс и находит расположение ресурсов по пути к файлу представления. В следующем примере кода демонстрируется использование реализации `IViewLocalizer` по умолчанию:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Реализация `IViewLocalizer` по умолчанию находит файл ресурсов по имени файла представления.

| Возможности использовать глобальный общий файл ресурсов нет. | `ViewLocalizer` реализует средство локализации с помощью интерфейса `IHtmlLocalizer`, поэтому Razor не кодирует локализованную строку в HTML. |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Вы можете параметризовать строки ресурсов, и `IViewLocalizer` будет кодировать в HTML параметры, но не строки ресурсов.

Рассмотрим следующую разметку Razor:

Файл ресурсов на французском языке может содержать следующие ресурсы:

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Ключ

Значение Преобразованное для просмотра представление будет содержать разметку HTML из файла ресурсов.

* **Примечание.** Как правило, требуется локализовать только текст, но не код HTML.
* Чтобы использовать в представлении общий файл ресурсов, внедрите `IHtmlLocalizer<T>`:

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

Локализация DataAnnotations Сообщения об ошибках DataAnnotations локализуются с помощью `IStringLocalizer<T>`.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>При использовании параметра `ResourcesPath = "Resources"` сообщения об ошибках в `RegisterViewModel` могут сохраняться по одному из следующих путей:

*Resources/ViewModels.Account.RegisterViewModel.fr.resx*

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

*Resources/ViewModels/Account/RegisterViewModel.fr.resx* В ASP.NET Core MVC 1.1.0 и более поздних версиях атрибуты, не относящиеся в проверке, локализуются.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>В ASP.NET Core MVC 1.0 **не** производится поиск локализованных строк для атрибутов, не относящихся к проверке.

### <a name="supportedcultures-and-supporteduicultures"></a>Использование одной строки ресурса для нескольких классов

В следующем коде показано, как использовать одну строку ресурса для атрибутов проверки с несколькими классами: В предыдущем коде `SharedResource` — это класс, соответствующий файлу RESX, в котором хранятся сообщения о проверке. При таком подходе DataAnnotations будут использовать только `SharedResource`, а не ресурс для каждого класса. Предоставление локализованных ресурсов для поддерживаемых языков и региональных параметров SupportedCultures и SupportedUICultures ASP.NET Core позволяет указывать два значения языка и региональных параметров: `SupportedCultures` и `SupportedUICultures`. Объект [CultureInfo](/dotnet/api/system.globalization.cultureinfo) для `SupportedCultures` определяет результаты функций, зависящих от языка и региональных параметров: форматирование дат, времени, чисел, денежных единиц и т. д. `SupportedCultures` также определяет порядок сортировки текста, соглашения о регистре символов и способы сравнения строк. Дополнительные сведения о получении сервером значения языка и региональных параметров см. в описании свойства [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).

## <a name="resource-files"></a>Значение `SupportedUICultures` определяет то, какие строки переводов (в файле *RESX*) ищет объект [ResourceManager](/dotnet/api/system.resources.resourcemanager).

`ResourceManager` ищет связанные с языком и региональными параметрами строки, которые определяются значением `CurrentUICulture`. Каждый поток в .NET имеет объекты `CurrentCulture` и `CurrentUICulture`. ASP.NET Core проверяет эти значения при обработке функций, зависящих от языка и региональных параметров. Например, если для текущего потока заданы язык и региональные параметры "en-US" (английский, США), метод `DateTime.Now.ToLongDateString()` выводит строку "Thursday, February 18, 2016", но если `CurrentCulture` имеет значение "ru-RU" (русский, Россия), выводится строка "четверг, 18 февраля 2016 г." Файлы ресурсов

1. Файл ресурсов — это полезное средство для отделения локализуемых строк от кода.

    ![Переведенные строки на языках, отличных от языка по умолчанию, содержатся в отдельных файлах ресурсов с расширением *RESX*. Например, вам нужно создать файл ресурсов для испанского языка с именем *Welcome.es.resx*, который будет содержать переведенные строки.](localization/_static/newi.png)

2. "es" — это код испанского языка.

    ![Чтобы создать этот файл ресурсов в Visual Studio, выполните указанные ниже действия.](localization/_static/res.png)

3. В **обозревателе решений** щелкните правой кнопкой мыши папку, которая будет содержать файл ресурсов, а затем выберите пункты **Добавить** > **Новый элемент**.

    ![Вложенное контекстное меню: в обозревателе решений контекстное меню открывается для папки ресурсов.](localization/_static/hola.png)

    Второе контекстное меню с выделенными пунктами "Добавить" и "Новый элемент".

    ![В поле **Поиск установленных шаблонов** введите слово "ресурс" и укажите имя файла.](localization/_static/se.png)

## <a name="resource-file-naming"></a>Диалоговое окно ''Добавление нового элемента''

Введите значение ключа (строку на исходном языке) в столбце **Имя** и переведенную строку в столбце **Значение**. Файл Welcome.es.resx (файлов ресурсов с приветствиями на испанском языке) со словом "Hello" в столбце "Имя" и словом "Hol"a ("Hello" на испанском) в столбце "Значение" В Visual Studio отобразится файл *Welcome.es.resx*. Обозреватель решений с файлом ресурсов на испанском языке Именование файлов ресурсов

Имена ресурсов представляют собой полные имена типов соответствующего класса за исключением имени сборки. Например, ресурс на французском языке в проекте, главная сборка которого имеет имя `LocalizationWebsite.Web.dll`, для класса `LocalizationWebsite.Web.Startup` будет иметь имя *Startup.fr.resx*. Ресурс для класса `LocalizationWebsite.Web.Controllers.HomeController` будет иметь имя *Controllers.HomeController.fr.resx*. Если пространство имен целевого класса не совпадает с именем сборки, необходимо использовать полное имя типа. Например, в образце проекта ресурс для типа `ExtraNamespace.Tools` будет иметь имя *ExtraNamespace.Tools.fr.resx*. В образце проекта метод `ConfigureServices` присваивает свойству `ResourcesPath` значение "Resources", поэтому относительный путь к файлу ресурсов на французском языке для контроллера домашней страницы в проекте будет иметь вид *Resources/Controllers.HomeController.fr.resx*.

| Кроме того, для упорядочения файлов ресурсов можно использовать папки. | Для контроллера домашней страницы путь будет иметь вид *Resources/Controllers/HomeController.fr.resx*. |
| ------------   | ------------- |
| Если параметр `ResourcesPath` не используется, файл *RESX* будет находиться в базовом каталоге проекта. | Файл ресурса для `HomeController` будет иметь имя *Controllers.HomeController.fr.resx*.  |
| Выбор соглашения об именовании на основе точечной нотации или пути зависит от того, как следует упорядочивать файлы ресурсов.  | Имя ресурса |
|    |     |

Точечная нотация или путь Resources/Controllers.HomeController.fr.resx Точки Resources/Controllers/HomeController.fr.resx

* Path

* Файлы ресурсов, для которых используется директива `@inject IViewLocalizer` в представлениях Razor, следуют той же модели.

Файлу ресурсов для представления может присваиваться имя на основе либо точечной нотации, либо пути.

### <a name="rootnamespaceattribute"></a>В файле ресурсов для представления Razor имитируется путь к связанному файлу представления. 

Допустим, свойству `ResourcesPath` присвоено значение "Resources". В этом случае файл ресурсов на французском языке, связанный с представлением *Views/Home/About.cshtml*, может иметь одно из следующих имен: 

> [!WARNING]
> Resources/Views/Home/About.fr.resx Resources/Views.Home.About.fr.resx 

Если параметр `ResourcesPath` не используется, файл *RESX* для представления будет находиться в той же папке, что и представление.

* RootNamespaceAttribute
* Атрибут [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) содержит корневое пространство имен сборки, если корневое пространство имен сборки отличается от имени сборки. Это может произойти, если имя проекта — недопустимый идентификатор .NET. 

Например, `my-project-name.csproj` будет использовать корневое пространство имен `my_project_name` и имя сборки `my-project-name`, что повлечет эту ошибку.

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Если корневое пространство имен сборки отличается от имени сборки

## <a name="culture-fallback-behavior"></a>Локализация не работает по умолчанию.

Локализация завершается сбоем из-за метода поиска ресурсов в сборке. `RootNamespace` — это значение во время сборки, которое недоступно выполняющемуся процессу. Если `RootNamespace` отличается от `AssemblyName`, включите следующее в файл *AssemblyInfo.cs* (со значениями параметров, замененными фактическими значениями). Приведенный выше код обеспечивает успешное разрешение RESX-файлов. Резервный язык и региональные параметры При поиске ресурса локализации использует резервный язык и региональные параметры.

Если запрошенный язык и региональные параметры не найдены, используется родительский язык и региональные параметры. Кстати, родительский язык и региональные параметры представляют свойство [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent).

* Обычно (но не всегда) это означает удаление национального символа из ISO.
* Например, диалект испанского в Мексике — es-MX.
* Родительский элемент "es" (испанский) не относится к той или иной стране.

Допустим, сайт получает запрос на ресурс "Приветствие" с использованием языка и региональных параметров fr-CA. Система локализации ищет в следующих ресурсах по порядку и выбирает первое совпадение: *Welcome.fr-CA.resx*

### <a name="generate-resource-files-with-visual-studio"></a>*Welcome.fr.resx*

*Welcome.resx* (если `NeutralResourcesLanguage` — fr-CA) Если вы удалите указатель языка и региональных параметров ".fr" и при этом задан французский язык и региональные параметры, будет считан файл ресурсов по умолчанию и строки локализуются. Диспетчер ресурсов назначает ресурс по умолчанию или резервный ресурс на тот случай, когда соответствия запрошенному языку и региональным параметрам не найдено. Если нужно, чтобы при отсутствии ресурса для запрошенного языка и региональных параметров просто возвращался ключ, не используйте файл ресурсов по умолчанию. Создание файлов ресурсов с помощью Visual Studio

### <a name="add-other-cultures"></a>Если вы создаете в Visual Studio файл ресурсов, в имени которого не указаны язык и региональные параметры (например, *Welcome.resx*), Visual Studio создаст класс C# со свойством для каждой строки.

В ASP.NET Core обычно требуется иное поведение. Как правило, файл ресурсов *RESX* по умолчанию (файл *RESX* без указания языка и региональных параметров) не используется. Мы рекомендуем создать файл *RESX* с указанием языка и региональных параметров (например, *Welcome.fr.resx*).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>При создании файла *RESX* с указанием языка и региональных параметров среда Visual Studio не создает файл класса.

### <a name="configure-localization"></a>Добавление других языков и региональных параметров

Каждое сочетание языка и региональных параметров (кроме языка по умолчанию) требует уникального файла ресурсов.

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* Создавая файлы ресурсов для разных языков, языковых стандартов и региональных параметров, вы включаете в их имена коды языков ISO (например, **en-us**, **ru-ru** и **fr-ca**). Эти коды ISO помещаются между именем файла и расширением *RESX*, например *Welcome.es-MX.resx* (испанский, Мексика).

* реализацию стратегии по выбору языка и региональных параметров для каждого запроса. Настройка локализации Локализация настраивается в методе `Startup.ConfigureServices`:

* Метод `AddLocalization` добавляет службы локализации в контейнер служб.

### <a name="localization-middleware"></a>В приведенном выше коде также задается путь к ресурсам "Resources".

Метод `AddViewLocalization` добавляет поддержку файлов локализованных представлений. В этом примере локализация образца представления основана на суффиксе файла представления, например "fr" в файле *Index.fr.cshtml*.

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Метод `AddDataAnnotationsLocalization` добавляет поддержку локализованных сообщений проверки `DataAnnotations` посредством абстракций `IStringLocalizer`. Промежуточный слой локализации Текущий язык и региональные параметры в запросе задаются в [промежуточном слое](xref:fundamentals/middleware/index) локализации.

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

Промежуточный слой локализации включается в методе `Startup.Configure`. Промежуточный слой локализации должен настраиваться перед другими промежуточными слоями, которые могут проверять язык и региональные параметры запроса (например, `app.UseMvcWithDefaultRoute()`). `UseRequestLocalization` инициализирует объект `RequestLocalizationOptions`.

### <a name="querystringrequestcultureprovider"></a>При каждом запросе проверяется список поставщиков `RequestCultureProvider` в объекте `RequestLocalizationOptions` и используется первый поставщик, который может успешно определить язык и региональные параметры запроса.

Поставщики по умолчанию берутся из класса `RequestLocalizationOptions`: В списке по умолчанию поставщики следуют от наиболее конкретных до наиболее общих. Далее в этой статье вы узнаете, как можно изменить этот порядок и даже добавить пользовательский поставщик языка и региональных параметров. Если ни один из поставщиков не может определить язык и региональные параметры запроса, используется `DefaultRequestCulture`. QueryStringRequestCultureProvider

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Некоторые приложения используют строку запроса для указания [языка и региональных параметров самого приложения и пользовательского интерфейса](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx). Если в приложении для этого применяется файл cookie или заголовок Accept-Language, добавление строки запроса к URL-адресу может быть полезным в целях отладки и тестирования кода.

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>По умолчанию поставщик `QueryStringRequestCultureProvider` регистрируется в качестве первого поставщика локализации в списке `RequestCultureProvider`.

Вы передаете параметры строки запроса `culture` и `ui-culture`. В следующем примере в качестве языка и региональных параметров (язык и регион) задаются испанский язык и Мексика:

Если передан только один из двух параметров (`culture` или `ui-culture`), поставщик строки запроса задаст с его помощью оба значения. Например, если передан только язык и региональные параметры, будут заданы оба значения `Culture` и `UICulture`:

CookieRequestCultureProvider

    c=en-UK|uic=en-US

Рабочие приложения часто предоставляют механизм для указания языка и региональных параметров с помощью соответствующего файла cookie ASP.NET Core.

### <a name="the-accept-language-http-header"></a>Чтобы создать файл cookie, используйте метод `MakeCookieValue`.

`CookieRequestCultureProvider` `DefaultCookieName` возвращает имя файла cookie по умолчанию, с помощью которого отслеживается предпочтительный язык и региональные параметры пользователя. Имя файла cookie по умолчанию — `.AspNetCore.Culture`. Файл cookie имеет формат `c=%LANGCODE%|uic=%LANGCODE%`, где `c` — это `Culture`, а `uic` — это `UICulture`, например: Если указаны либо только язык и региональные параметры, либо только язык и региональные параметры пользовательского интерфейса, это значение будет использоваться для обоих параметров.

### <a name="set-the-accept-language-http-header-in-ie"></a>Заголовок HTTP Accept-Language

1. [Заголовок Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) может задаваться в большинстве браузеров и изначально предназначался для указания языка пользователя.

2. Он показывает, какой язык был выбран в браузере или унаследован от базовой операционной системы.

    ![Заголовок HTTP Accept-Language в запросе из браузера — это не самый надежный способ определения предпочтительного языка пользователя (см. статью [Настройка языковых предпочтений в браузере](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).](localization/_static/lang.png)

3. В рабочем приложении должна быть возможность выбора языка и региональных параметров пользователем.

4. Задание заголовка HTTP Accept-Language в Internet Explorer

5. Щелкните значок шестеренки и выберите пункт **Свойства браузера**.

6. Нажмите кнопку **Языки**.

### <a name="the-content-language-http-header"></a>Свойства браузера

Нажмите **Задать предпочитаемые языки**.

 - Нажмите **Добавить язык**.
 - Добавьте язык.

Выберите язык, а затем нажмите кнопку **Вверх**.

Заголовок HTTP Content-Language

Заголовок сущности [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language):

 - Используется для описания языков, предназначенных для аудитории.
 - Позволяет пользователю различать в соответствии с предпочтительным языком пользователя.

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a>Заголовки сущностей используются как в HTTP-запросах, так и в ответах.

Заголовок `Content-Language` можно добавить, задав свойство `ApplyCurrentCultureToResponseHeaders`. Добавление заголовка `Content-Language`: Позволяет RequestLocalizationMiddleware задать заголовок `Content-Language` с `CurrentUICulture`.

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

Устраняет необходимость явной установки заголовка ответа `Content-Language`.

### <a name="set-the-culture-programmatically"></a>Использование пользовательского поставщика

Предположим, необходимо, чтобы ваши клиенты могли сохранять информацию о своем языке и региональных параметрах в ваших базах данных. Можно написать поставщик, который будет искать эти значения для пользователя.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

В следующем примере кода показано, как добавить пользовательский поставщик:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

Для добавления или удаления поставщиков локализации используйте объект `RequestLocalizationOptions`.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Задание языка и региональных параметров программным образом В образце проекта **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть пользовательский интерфейс для задания значения `Culture`.

## <a name="model-binding-route-data-and-query-strings"></a>Файл *Views/Shared/_SelectLanguagePartial.cshtml* позволяет выбрать язык и региональные параметры из списка поддерживаемых:

Файл *Views/Shared/_SelectLanguagePartial.cshtml* добавляется в раздел `footer` файла макета, поэтому он доступен всем представлениям:

## <a name="globalization-and-localization-terms"></a>Метод `SetLanguage` задает файл cookie языка и региональных параметров.

Подключить файл *_SelectLanguagePartial.cshtml* к образцу кода этого проекта нельзя. В проекте **Localization.StarterWeb** в [GitHub](https://github.com/aspnet/entropy) есть код для передачи объекта `RequestLocalizationOptions` в частичное представление Razor посредством контейнера [внедрения зависимостей](dependency-injection.md). Данные маршрутов привязки модели и строки запросов

См. раздел [Поведение глобализации для данных маршрутов привязки модели и строк запросов](xref:mvc/models/model-binding#glob).

Термины, относящиеся к глобализации и локализации Процесс локализации приложения требует базового понимания распространенных кодировок, часто используемых при разработке современного программного обеспечения, и связанных с ними проблем. Хотя на всех компьютерах текст сохраняется в виде цифр (кодов), в разных системах эти коды для одного и того же текста различаются. Под процессом локализации понимается перевод пользовательского интерфейса приложения для определенного языка и региональных параметров.

[Возможность локализации](/dotnet/standard/globalization-localization/localizability-review) — это определяемое на промежуточной стадии состояние готовности глобализованного приложения к локализации. Формат [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) для названия языка и региональных параметров имеет вид `<languagecode2>-<country/regioncode2>`, где `<languagecode2>` — это код языка, а `<country/regioncode2>` — код субкультуры. Примеры: `es-CL` для испанского языка (Чили), `en-US` для английского языка (США), `en-AU` для английского языка (Австралия).

[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) — это комбинация двухбуквенного кода культуры ISO 639 в нижнем регистре (он связан с языком) и двухбуквенного кода субкультуры ISO 3166 в верхнем регистре (он связан со страной или регионом).

* См. статью [Имя языка и региональных параметров](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).
* Вместо слова "интернационализация" (internationalization) часто используют аббревиатуру "I18N".
* В аббревиатуру включаются первая и последняя буквы слова, а также число букв между ними, то есть 18 — это число букв между первой ("I") и последней ("N") буквами.
* То же самое касается глобализации (Globalization — G11N) и локализации (Localization — L10N).
* Термины: Глобализация (G11N): процесс подготовки приложения к поддержке различных языков и регионов.
* Локализация (L10N): процесс настройки приложения для работы с конкретным языком и регионом. Интернационализация (I18N): описывает глобализацию и локализацию.
* Язык и региональные параметры: обозначает язык и (необязательно) регион. Нейтральный язык и региональные параметры: язык и региональные параметры, для которых указан язык, но не регион.
* (например, "en", "es").

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>Конкретный язык и региональные параметры: язык и региональные параметры, для которых указаны язык и регион.

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* (например, "en-US", "en-GB", "es-CL").
* Родительский язык и региональные параметры: нейтральные язык и региональные параметры, содержащие конкретные язык и региональные параметры.
* (например, "en" — это родительские язык и региональные параметры для "en-US" и "en-GB").
* Языковой стандарт: тот же, что и язык и региональные параметры.
* Дополнительные ресурсы

::: moniker-end
