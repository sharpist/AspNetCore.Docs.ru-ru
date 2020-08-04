---
title: Ведение журнала в .NET Core и ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать платформу ведения журналов, предоставляемую пакетом NuGet Microsoft.Extensions.Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330771"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Ведение журнала в .NET Core и ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Авторы: [Кирк Ларкин (Kirk Larkin)](https://twitter.com/serpent5), [Юрген Гутч (Juergen Gutsch)](https://github.com/JuergenGutsch) и [Рик Андерсн (Rick Anderson)](https://twitter.com/RickAndMSFT)

.NET Core поддерживает API ведения журналов, который работает с разными встроенными и сторонними поставщиками. В этой статье описано, как использовать в коде API ведения журналов, работающего со встроенными поставщиками.

Большинство примеров кода, приведенных в этой статье, взяты из приложений ASP.NET Core. Части этих фрагментов кода, относящиеся к ведению журнала, применимы ко всем приложениям .NET Core, использующим [универсальный узел](xref:fundamentals/host/generic-host). Шаблоны веб-приложений ASP.NET Core используют универсальный узел.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([как скачивать](xref:index#how-to-download-a-sample))

<a name="lp"></a>

## <a name="logging-providers"></a>Поставщики ведения журнала

Поставщики ведения журнала обеспечивают хранение журналов, за исключением поставщика `Console`, который служит для их отображения. Например, поставщик Azure Application Insights хранит журналы в Azure Application Insights. Вы можете включить несколько поставщиков.

Шаблоны по умолчанию для веб-приложений ASP.NET Core:

* используют [универсальный узел](xref:fundamentals/host/generic-host);
* вызывают метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, который добавляет следующие поставщики ведения журнала:
  * [Консоль](#console)
  * [Отладка](#debug)
  * [EventSource](#event-source)
  * [EventLog](#welog): Только для Windows

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

В предыдущем коде показан класс `Program`, созданный с использованием шаблонов веб-приложений ASP.NET Core. В следующих разделах приводятся примеры, которые построены на основе шаблонов веб-приложений ASP.NET Core и используют универсальный узел. [Консольные приложения без размещения](#nhca) рассматриваются далее в этом документе.

Чтобы переопределить заданный по умолчанию набор поставщиков ведения журнала, добавленных с помощью `Host.CreateDefaultBuilder`, вызовите метод `ClearProviders` и добавьте необходимые поставщики. Например, приведенный ниже код

* вызывает метод <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> для удаления всех экземпляров <xref:Microsoft.Extensions.Logging.ILoggerProvider> из построителя;
* добавляет поставщик ведения журнала [Console](#console).

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

Сведения о других поставщиках см. в следующих статьях:

* [Встроенные поставщики ведения журнала](#bilp).
* [Сторонние поставщики ведения журнала](#third-party-logging-providers).

## <a name="create-logs"></a>Создание журналов

Чтобы создать журналы, используйте объект <xref:Microsoft.Extensions.Logging.ILogger%601> из [внедрения зависимостей](xref:fundamentals/dependency-injection).

В следующем примере происходит следующее:

* Создает средство ведения журнала `ILogger<AboutModel>`, которое использует *категорию* журналов с полным именем типа `AboutModel`. Категория ведения журналов представляет строку, которая связана с каждым журналом.
* Вызывает метод <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> для ведения журналов на уровне `Information`. *Уровень* ведения журналов определяет степень серьезности или важности записанного в журнал события.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

[Уровни](#log-level) и [категории](#log-category) рассматриваются подробнее далее в этом документе.

Дополнительные сведения о Blazor см. в статье [Создание журналов в Blazor и Blazor WebAssembly](#clib) в этом документе.

В разделах, посвященных [созданию журналов в классах Main и Startup](#clms), описывается создание журналов в `Main` и `Startup`.

## <a name="configure-logging"></a>Настройка журнала

Конфигурацию ведения журналов обычно предоставляет раздел `Logging` в файлах *appsettings*.`{Environment}` *.json*. Шаблоны веб-приложений ASP.NET Core создают следующий файл *appsettings.Development.json*:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

В приведенном выше документе JSON:

* Указаны категории `"Default"`, `"Microsoft"`и `"Microsoft.Hosting.Lifetime"`.
* Категория `"Microsoft"` применяется ко всем категориям, начинающимся с `"Microsoft"`. Например, этот параметр применяется к категории `"Microsoft.AspNetCore.Routing.EndpointMiddleware"`.
* Категория `"Microsoft"` задает ведение журналов на уровне `Warning` и более высоком.
* Категория `"Microsoft.Hosting.Lifetime"` является более детальной по сравнению с `"Microsoft"`, поэтому при выборе категории `"Microsoft.Hosting.Lifetime"` ведение журналов осуществляется на уровне "Информация" и более высоком.
* Поскольку конкретный поставщик журналов не указан, `LogLevel` применяется ко всем включенным поставщикам, за исключением [Windows EventLog](#welog).

Свойство `Logging` может иметь свойство <xref:Microsoft.Extensions.Logging.LogLevel> и свойства поставщика журналов. Свойство `LogLevel` указывает минимальный [уровень](#log-level) журнала для выбранных категорий. В приведенном выше коде JSON заданы уровни ведения журнала `Information` и `Warning`. `LogLevel` определяет степень серьезности журнала и задается в диапазоне от 0 до 6:

`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 и `None` = 6.

Если задан `LogLevel`, журналы будут вестись для сообщений с указанным и более высокими уровнями. В приведенном выше коде JSON задается ведение журналов для категории `Default` для сообщений с уровнем `Information` и более высоким. Например, в журнал записываются сообщения с уровнями `Information`, `Warning`, `Error` и `Critical`. Если `LogLevel` не задан, по умолчанию устанавливается уровень ведения журналов `Information`. Дополнительные сведения см. в статье [Уровни ведения журналов](#llvl).

Свойство поставщика может задавать свойство `LogLevel`. Свойство `LogLevel` поставщика определяет уровень ведения журналов для этого поставщика и переопределяет любые другие не относящиеся к поставщику параметры ведения журналов. Рассмотрите следующий файл *appsettings.json*:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

Параметры в `Logging.{providername}.LogLevel` переопределяют параметры в `Logging.LogLevel`. В приведенном выше коде JSON для поставщика `Debug` задается уровень ведения журнала по умолчанию `Information`:

`Logging:Debug:LogLevel:Default:Information`

Приведенный выше параметр задает уровень ведения журнала `Information` для всех категорий `Logging:Debug:`, за исключением `Microsoft.Hosting`. Если задана конкретная категория, она переопределяет категорию по умолчанию. В приведенном выше коде JSON категории `Logging:Debug:LogLevel` `"Microsoft.Hosting"` и `"Default"` переопределяют параметры в `Logging:LogLevel`.

Минимальный уровень ведения журнала можно указать для:

* конкретных поставщиков.  Например: `Logging:EventSource:LogLevel:Default:Information`
* конкретных категорий. Например: `Logging:LogLevel:Microsoft:Warning`
* всех поставщиков и всех категорий: `Logging:LogLevel:Default:Warning`

***Любые*** журналы с уровнем ниже минимального:

* не передаются поставщику;
* не записываются в журнал и не отображаются.

Чтобы отключить все журналы, укажите [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel). `LogLevel.None` имеет значение 6, то есть выше `LogLevel.Critical` (5).

Если поставщик поддерживает [области журналов](#logscopes), `IncludeScopes` определяет, включены ли они. Дополнительные сведения см. в статье [Области журналов](#logscopes).

Следующий файл *appsettings.json* содержит все поставщики, включенные по умолчанию:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

В предыдущем примере:

* Категории и уровни не являются предлагаемыми значениями. Этот пример представлен с целью продемонстрировать все поставщики по умолчанию.
* Параметры в `Logging.{providername}.LogLevel` переопределяют параметры в `Logging.LogLevel`. Например, уровень в `Debug.LogLevel.Default` переопределяет уровень в `LogLevel.Default`.
* Каждый поставщик по умолчанию использует *псевдоним*. Каждый поставщик определяет *псевдоним*, используемый в конфигурации вместо полного имени типа. Ниже приведены псевдонимы встроенных поставщиков:
  * Консоль
  * Отладка
  * EventSource
  * EventLog
  * AzureAppServicesFile
  * AzureAppServicesBlob
  * ApplicationInsights

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a>Настройка уровня ведения журнала с помощью командной строки, переменных среды и других способов конфигурации

Уровень ведения журнала может задаваться любыми [поставщиками конфигурации](xref:fundamentals/configuration/index). 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Приведенные ниже команды:

* присваивают ключу среды `Logging:LogLevel:Microsoft` значение `Information` в Windows;
* проверяют параметры при использовании приложения, созданного на основе шаблонов веб-приложений ASP.NET Core. Команда `dotnet run` должна выполняться в каталоге проекта после использования `set`.

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

Приведенный выше параметр среды:

* задается только в процессах, запускаемых из командного окна, в котором он был установлен;
* не будет считываться браузерами, запущенными в Visual Studio.

Приведенная ниже команда [Setx](/windows-server/administration/windows-commands/setx) также задает ключ среды и значение в Windows. В отличие от `set`, параметры `setx` сохраняются. Параметр `/M` задает переменную в системной среде. Если параметр `/M` не используется, задается переменная среды пользователя.

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

В [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) выберите **Новый параметр приложения** на странице **Параметры > Конфигурация**. Параметры приложения Службы приложений Azure:

* Шифруются, когда они неактивны, и передаются по зашифрованному каналу.
* Предоставляются как переменные среды.

Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Дополнительные сведения о настройке значений конфигурации ASP.NET Core с помощью переменных среды см. в статье [Переменные среды](xref:fundamentals/configuration/index#environment-variables). Дополнительные сведения об использовании других источников конфигурации, включая командную строку, Azure Key Vault, службу конфигурации приложений Azure, различные форматы файлов и другие, см. в статье <xref:fundamentals/configuration/index>.

## <a name="how-filtering-rules-are-applied"></a>Применение правил фильтрации

При создании объекта <xref:Microsoft.Extensions.Logging.ILogger%601> объект <xref:Microsoft.Extensions.Logging.ILoggerFactory> выбирает одно правило для каждого поставщика, которое будет применено к этому средству ведения журналов. Все сообщения, записываемые с помощью экземпляра `ILogger`, фильтруются на основе выбранных правил. Самое подробное правило для каждой пары поставщика и категории выбирается из списка доступных правил.

При создании `ILogger` для данной категории для каждого поставщика используется приведенный далее алгоритм:

* Выберите все правила, которые соответствуют поставщику или его псевдониму. Если ничего не найдено, выберите все правила с пустым поставщиком.
* В результатах предыдущего шага выберите правила с самым длинным соответствующим префиксом категории. Если ничего не найдено, выберите все правила, которые не указывают категорию.
* Если выбрано несколько правил, примите **последнее**.
* Если правила не выбраны, используйте `MinimumLevel`.

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a>Запись в журнал выходных данных dotnet run и Visual Studio

Журналы, созданные с помощью [установленных по умолчанию поставщиков ведения журнала](#lp), отображаются:

* В Visual Studio
  * в окне выходных данных отладки при отладке;
  * в окне веб-сервера ASP.NET Core.
* В окне консоли при запуске приложения с помощью команды `dotnet run`.

Журналы, которые начинаются с категорий Microsoft, входят в код платформы ASP.NET Core. В ASP.NET Core и коде приложения используются один и тот же API ведения журнала и одни и те же поставщики.

<a name="lcat"></a>

## <a name="log-category"></a>Категория журнала

При создании объекта `ILogger` указывается *категория*. Эта категория входит в состав каждого сообщения журнала, создаваемого этим экземпляром `ILogger`. Строка категории является необязательной, однако по соглашению следует использовать имя класса. Например, в контроллере может использоваться имя `"TodoApi.Controllers.TodoController"`. Веб-приложения ASP.NET Core используют `ILogger<T>` для автоматического получения экземпляра `ILogger`, который использует полное имя типа `T` в качестве категории:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

Чтобы явно указать категорию, вызовите `ILoggerFactory.CreateLogger`:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

Использование `ILogger<T>` эквивалентно вызову `CreateLogger` с полным именем типа `T`.

<a name="llvl"></a>

## <a name="log-level"></a>Уровень ведения журнала

В следующей таблице перечислены значения <xref:Microsoft.Extensions.Logging.LogLevel>, используемый для удобства метод расширения `Log{LogLevel}`, а также приводятся сведения о предполагаемом применении:

| LogLevel | Значение | Метод | Описание |
| -------- | ----- | ------ | ----------- |
| [Трассировка](xref:Microsoft.Extensions.Logging.LogLevel) | 0 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | Содержит наиболее подробные сообщения. Эти сообщения могут содержать конфиденциальные данные приложения. Эти сообщения по умолчанию отключены, и их ***никогда*** не следует включать в рабочей среде. |
| [Отладка](xref:Microsoft.Extensions.Logging.LogLevel) | 1 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | Используется для отладки и разработки. В рабочей среде следует использовать с осторожностью из-за большого объема. |
| [Информация](xref:Microsoft.Extensions.Logging.LogLevel) | 2 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | Отслеживание общего потока работы приложения. Может использоваться в долгосрочных целях. |
| [Предупреждение](xref:Microsoft.Extensions.Logging.LogLevel) | 3 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | Для нестандартных или непредвиденных событий. Обычно содержит ошибки или условия, которые не приводят к сбою приложения. |
| [Ошибка](xref:Microsoft.Extensions.Logging.LogLevel) | 4 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | Для ошибок и исключений, которые не могут быть обработаны. Эти сообщения указывают на сбой текущих операции или запроса, а не на ошибку уровня приложения. |
| [Критическая](xref:Microsoft.Extensions.Logging.LogLevel) | 5 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | Для сбоев, которые требуют немедленного внимания. Примеры: потеря данных, нехватка места на диске. |
| [None](xref:Microsoft.Extensions.Logging.LogLevel) | 6 | | Указывает, что категория ведения журнала не должна подразумевать запись каких-либо сообщений. |

В приведенной выше таблице значения `LogLevel` приведены в порядке от самого низкого к самому высокому уровню серьезности.

Первый параметр метода [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions), <xref:Microsoft.Extensions.Logging.LogLevel>, указывает на степень серьезности журнала. Вместо вызова `Log(LogLevel, ...)` большинство разработчиков вызывают методы расширения [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions). Методы расширения `Log{LogLevel}` [вызывают метод Log и задают значение LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs). Например, следующие два вызова ведения журнала функционально эквивалентны и позволяют получить одинаковые журналы:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

`MyLogEvents.TestItem` — это идентификатор события. `MyLogEvents` является частью примера приложения и отображается в разделе [Идентификатор события журнала](#leid).

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

Следующий код создает журналы `Information`и `Warning`:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

В коде выше первый параметр, `MyLogEvents.GetItem`, — это `Log{LogLevel}`[идентификатор события журнала](#leid). Второй параметр — это шаблон сообщения с заполнителями для значений аргументов, предоставляемых оставшимися параметрами метода. Параметры метода рассматриваются более подробно в разделе, посвященном [шаблону сообщений](#lmt), далее в этом документе.

С помощью вызова соответствующего метода `Log{LogLevel}` можно управлять объемом выходных данных, записываемых на определенный носитель. Пример:

* В рабочей среде:
  * При ведении журнала на уровнях `Trace` или `Information` создается большой объем подробных сообщений журнала. Чтобы контролировать затраты и не превысить лимиты объема хранилища данных, записывайте сообщения на уровнях `Trace` и `Information` в хранилище данных с низкими затратами и большим объемом. Рассмотрите возможность ограничения уровней `Trace` и `Information` конкретными категориями.
  * При ведении журналов на уровнях с `Warning` по `Critical` создается немного сообщений.
    * При этом стоимость и ограничения хранения, как правило, не важны.
    * Меньший объем журналов позволяет выбирать среди большего количества вариантов хранения данных.
* В среде разработки:
  * Задайте значение `Warning`.
  * Добавляйте сообщения уровней `Trace` или `Information` при устранении неполадок. Чтобы ограничить объем выходных данных, задавайте уровни `Trace` или `Information` только для исследуемых категорий.

ASP.NET Core создает журналы для событий платформы. В качестве примера рассмотрим выходные данные журнала для:

* приложения страниц Razor, созданного на основе шаблонов ASP.NET Core;
* уровня ведения журналов `Logging:Console:LogLevel:Microsoft:Information`;
* перехода на страницу сведений о конфиденциальности:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

Приведенный ниже код JSON задает уровень `Logging:Console:LogLevel:Microsoft:Information`:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a>Идентификатор события журнала

Каждый журнал может указывать *идентификатор события*. В этом примере приложения используется класс `MyLogEvents` для определения идентификаторов событий:

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Идентификатор события связывает набор событий. Например, все журналы, связанные с отображением списка элементов на странице, могут иметь значение 1001.

Поставщик ведения журналов может хранить идентификатор события в поле идентификатора, в сообщении журнала, или вообще не хранить. Поставщик Debug не отображает идентификаторы событий. Поставщик Console отображает идентификаторы событий в квадратных скобках после категории:

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

Некоторые поставщики ведения журнала хранят идентификатор события в поле, что позволяет выполнять фильтрацию по идентификатору.

<a name="lmt"></a>

## <a name="log-message-template"></a>Шаблон сообщения журнала
<!-- Review, Each log API uses a message template. -->
Каждый API ведения журнала использует шаблон сообщения. Шаблон сообщения может содержать заполнители, для которых предоставляются аргументы. Используйте для заполнителей имена, а не числа.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Параметры, используемые для предоставления значений, определяются порядком заполнителей, а не их именами. В приведенном ниже коде имена параметров идут не по порядку в шаблоне сообщения:

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Приведенный выше код создает сообщение журнала со значениями параметров в определенном порядке:

```text
Parameter values: param1, param2
```

Такой подход позволяет поставщикам ведения журнала реализовывать [семантическое или структурированное ведение журналов](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging). Сами аргументы передаются в систему ведения журналов, а не только в отформатированный шаблон сообщения. Это позволяет поставщикам ведения журнала хранить значения параметров как поля. Например, рассмотрим следующий метод средства ведения журнала:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Например, при ведении журнала в хранилище таблиц Azure:

* каждая сущность таблицы Azure может иметь свойства `ID` и `RequestTime`;
* использование таблиц со свойствами позволяет упростить выполнение запросов к данным журнала. Например, с помощью запроса можно находить все журналы в пределах определенного диапазона `RequestTime`, не анализируя время ожидания текстового сообщения.

## <a name="log-exceptions"></a>Запись исключений в журнал

Методы средства ведения журнала имеют перегрузки, которые принимают параметр исключения:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

Принципы записи исключений в журнал зависят от конкретного поставщика.

### <a name="default-log-level"></a>Уровень ведения журнала по умолчанию

Если не задан уровень ведения журнала по умолчанию, то по умолчанию используется уровень `Information`.

Рассмотрим в качестве примера следующее веб-приложение:

* приложение создано на основе шаблонов веб-приложений ASP.NET;
* файлы *appsettings.json* и *appsettings.Development.json* были удалены или переименованы.

В рамках приведенной выше конфигурации при переходе на страницу сведений о конфиденциальности или домашнюю страницу создается множество сообщений с уровнем `Trace`, `Debug` и `Information`, в имени категории которых используется `Microsoft`.

В следующем коде задается уровень ведения журнала по умолчанию в том случае, если этот уровень не определен в конфигурации:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
Как правило, уровни ведения журнала следует задавать не в коде, а в конфигурации.

### <a name="filter-function"></a>Функция фильтрации

Функция фильтрации вызывается для всех поставщиков и категорий, которым в конфигурации и (или) коде не назначены правила:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

Приведенный выше код отображает журналы консоли, если категория содержит `Controller` или `Microsoft` и задан уровень ведения журнала `Information` или выше.

Как правило, уровни ведения журнала следует задавать не в коде, а в конфигурации.

## <a name="aspnet-core-and-ef-core-categories"></a>Категории ASP.NET Core и EF Core

В следующей таблице приведены некоторые категории, используемые ASP.NET Core и Entity Framework Core с заметками о журналах:

| Категория                            | Примечания |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Общая диагностика ASP.NET Core. |
| Microsoft.AspNetCore.DataProtection | Распознанные, найденные и использованные ключи. |
| Microsoft.AspNetCore.HostFiltering  | Разрешенные узлы. |
| Microsoft.AspNetCore.Hosting        | Время начала и длительность выполнения HTTP-запросов. Определение загруженных начальных сборок размещения. |
| Microsoft.AspNetCore.Mvc            | Диагностика MVC и Razor. Привязка моделей, выполнение фильтра, компиляция представлений, выбор действия. |
| Microsoft.AspNetCore.Routing        | Перенаправление соответствующих сведений. |
| Microsoft.AspNetCore.Server         | Запуск, остановка и сохранение ответов. Сведения о сертификате HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | Обработанные файлы. |
| Microsoft.EntityFrameworkCore       | Общая диагностика Entity Framework Core. Действия и конфигурация базы данных, обнаружение изменений, переносы. |

Чтобы просмотреть в окне консоли другие категории, выполните следующее присвоение **appsettings.Development.json**:

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a>Области журналов

 *Область* может группировать набор логических операций. Эту возможность можно использовать для присоединения одних и тех же данных к каждому журналу, созданному как часть набора. Например, каждый журнал, созданный в ходе обработки транзакции, может включать идентификатор транзакции.

Область:

* имеет тип <xref:System.IDisposable>, который возвращается методом <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>;
* действует вплоть до удаления.

Области поддерживаются следующими поставщиками:

* `Console`
* [AzureAppServicesFile и AzureAppServicesBlob](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

Используйте область, заключив вызовы средства ведения журналов в блок `using`:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

Следующий код JSON предоставляет области для поставщика Console:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

Следующий код предоставляет области для поставщика Console:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

Как правило, параметры ведения журнала следует задавать не в коде, а в конфигурации.

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a>Встроенные поставщики ведения журналов

ASP.NET Core включает следующие поставщики ведения журнала:

* [Консоль](#console)
* [Отладка](#debug)
* [EventSource](#event-source)
* [EventLog](#welog)
* [AzureAppServicesFile и AzureAppServicesBlob](#azure-app-service)
* [ApplicationInsights](#azure-application-insights)

Дополнительные сведения о `stdout` и ведении журнала отладки с помощью модуля ASP.NET Core см. в статьях <xref:test/troubleshoot-azure-iis> и <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console"></a>Консоль

Поставщик `Console` выводит выходные данные в консоль. Дополнительные сведения о просмотре журналов `Console` в среде разработки см. в статье [Запись в журнал выходных данных dotnet run и Visual Studio](#dnrvs).

### <a name="debug"></a>Отладка

Поставщик `Debug` записывает выходные данные журнала с использованием класса [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug). При вызове методов `System.Diagnostics.Debug.WriteLine` выполняется запись в поставщик `Debug`.

В Linux расположение журнала поставщика `Debug` зависит от дистрибутива и может быть одним из следующих:

* */var/log/message*
* */var/log/syslog*

### <a name="event-source"></a>Источник события

Поставщик `EventSource` выполняет запись в кроссплатформенный источник событий с именем `Microsoft-Extensions-Logging`. В Windows поставщик использует [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

#### <a name="dotnet-trace-tooling"></a>Средства трассировки dotnet

Средство [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) — это универсальное кроссплатформенное средство командной строки, которое выполняет сбор трассировок .NET Core для запущенного процесса. Средство собирает данные поставщика <xref:Microsoft.Extensions.Logging.EventSource> с помощью <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.

Инструкции по установке см. в статье [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace).

Используйте средства трассировки dotnet, чтобы получить трассировку из приложения:

1. Запустите приложение с помощью команды `dotnet run`.
1. Определите идентификатор процесса (PID) приложения .NET Core:
   * В Windows воспользуйтесь одним из перечисленных ниже подходов:
     * Диспетчер задач (CTRL+ALT+DEL)
     * [Команда tasklist](/windows-server/administration/windows-commands/tasklist)
     * [Команда Powershell Get-Process](/powershell/module/microsoft.powershell.management/get-process)
   * В Linux используйте [команду pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).

   Найдите идентификатор процесса, имя которого совпадает с именем сборки приложения.

1. Выполните команду `dotnet trace`.

   Общий синтаксис команды:

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   При использовании командной оболочки PowerShell заключите значение `--providers` в одинарные кавычки (`'`):

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   На платформах, отличных от Windows, добавьте параметр `-f speedscope`, чтобы изменить формат выходного файла трассировки на `speedscope`.

   | Ключевое слово | Описание |
   | :-----: | ----------- |
   | 1       | Занесите в журнал метасобытия о `LoggingEventSource`. Не заносит в журнал события из `ILogger`). |
   | 2       | Включает событие `Message` при вызове `ILogger.Log()`. Предоставляет сведения в исходном виде (без форматирования). |
   | 4       | Включает событие `FormatMessage` при вызове `ILogger.Log()`. Предоставляет сведения в виде отформатированной строки. |
   | 8       | Включает событие `MessageJson` при вызове `ILogger.Log()`. Предоставляет представление аргументов в формате JSON. |

   | Уровень события | Описание     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   Записи `FilterSpecs` для `{Logger Category}` и `{Event Level}` представляют дополнительные условия фильтрации журналов. Отдельные записи `FilterSpecs` разделяются точкой с запятой (`;`).

   Пример использования командной оболочки Windows (**не** одинарные кавычки вокруг значения `--providers`):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   Предыдущая команда активирует:

   * Средство ведения журнала источника событий для создания форматированных строк (`4`) для ошибок (`2`).
   * Ведение журнала `Microsoft.AspNetCore.Hosting` на уровне ведения журнала `Informational` (`4`).

1. Остановите средства трассировки dotnet, нажав клавишу ENTER или CTRL+C.

   Трассировка сохраняется с именем *trace.nettrace* в папке, в которой выполняется команда `dotnet trace`.

1. Откройте трассировку с помощью [Perfview](#perfview). Откройте файл *trace.nettrace* и изучите события трассировки.

Если приложение не создает узел с `CreateDefaultBuilder`, добавьте [Поставщика источника событий](#event-source-provider) в конфигурацию ведения журнала приложения.

Дополнительные сведения можно найти в разделе

* [Трассировка для программы анализа производительности (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (документация по .NET Core)
* [Трассировка для программы анализа производительности (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (документация по репозиторию GitHub dotnet/diagnostics)
* [Класс LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (обозреватель API .NET)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [Источник ссылки LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): чтобы получить источник ссылки для другой версии, измените ветку на `release/{Version}`, где `{Version}` — это нужная версия ASP.NET Core.
* [Perfview](#perfview): полезный инструмент для просмотра трассировок источника событий.

#### <a name="perfview"></a>Perfview

Для сбора и просмотра данных журналов рекомендуется использовать [программу PerfView](https://github.com/Microsoft/perfview). Существуют и другие средства для просмотра журналов трассировки событий Windows, но PerfView обеспечивает максимальное удобство работы с событиями трассировки событий Windows, создаваемыми ASP.NET Core.

Чтобы настроить PerfView для сбора событий, регистрируемых этим поставщиком, добавьте строку `*Microsoft-Extensions-Logging` в список **Дополнительные поставщики**. Не пропустите символ `*` в начале строки.

<a name="welog"></a>

### <a name="windows-eventlog"></a>Windows EventLog

Поставщик `EventLog` отправляет выходные данные журнала в журнал событий Windows. В отличие от других поставщиков, поставщик `EventLog` ***не наследует*** параметры по умолчанию, не относящиеся к поставщику. Если параметры журнала `EventLog` не указаны, [по умолчанию используется уровень LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).

Чтобы регистрировать события с уровнем ниже <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, следует явно задать уровень ведения журнала. В следующем примере для журнала событий по умолчанию задается уровень <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

[Перегрузки AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) позволяют передавать <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>. Если `null` или не указан, используются следующие параметры по умолчанию:

* `LogName`. "Application"
* `SourceName`: ".NET Runtime"
* `MachineName`. Используется имя локального компьютера.

Следующий код изменяет `SourceName` со значения по умолчанию `".NET Runtime"` на `MyLogs`:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a>Служба приложений Azure

Пакет поставщика [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) записывает журналы в текстовые файлы в файловой системе приложения службы приложений Azure и в [хранилище больших двоичных объектов](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) в учетной записи хранения Azure.

Пакет поставщика не включен в общую платформу. Чтобы использовать поставщик, добавьте пакет поставщика в проект.

Для настройки параметров поставщика используйте <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> и <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, как показано в следующем примере:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

При развертывании в службе приложений Azure ваше приложение использует параметры в разделе [Журналы службы приложений](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) на странице **Служба приложений** на портале Azure. При обновлении следующих параметров изменения вступают в силу немедленно без перезапуска или повторного развертывания приложения:

* **Ведение журнала приложения (файловая система)** ;
* **Ведение журнала приложения (BLOB-объект)** .

По умолчанию файлы журнала находятся в папке *D:\\home\\LogFiles\\Application*, а имя файла по умолчанию — *diagnostics-yyyymmdd.txt*. Максимальный размер файла по умолчанию составляет 10 МБ, а максимальное количество сохраняемых по умолчанию файлов равно 2. Имя BLOB-объекта по умолчанию — *{имя_приложения}{метка_времени}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.

Этот поставщик работает только при выполнении проекта в среде Azure.

#### <a name="azure-log-streaming"></a>Потоковая передача журналов Azure

Потоковая передача журналов Azure позволяет просматривать активность журнала в реальном времени из следующих источников:

* сервер приложений;
* веб-сервер;
* трассировка неудачно завершенных запросов.

Настройка потоковой передачи журналов Azure

* Со страницы портала приложения перейдите на страницу **Журналы службы приложений**.
* **Включите** параметр **Ведение журнала приложения (файловая система)** .
* Выберите **уровень** ведения журнала. Этот параметр применяется только к потоковой передаче журналов Azure.

Перейдите на страницу **Поток журналов**, чтобы просмотреть журналы. Сообщения записываются в журнал с помощью интерфейса `ILogger`.

### <a name="azure-application-insights"></a>Azure Application Insights

Пакет поставщика [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) записывает журналы в [Azure Application Insights](/azure/azure-monitor/app/cloudservices). Служба Application Insights отслеживает веб-приложения и предоставляет средства для создания запросов и анализа данных телеметрии. Используя этого поставщика, вы сможете выполнять запросы к журналам и их анализ с помощью средств Application Insights.

Поставщик ведения журнала включается как зависимость [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Этот пакет предоставляет всю доступную телеметрию для ASP.NET Core. Если вы используете этот пакет, пакет поставщика устанавливать не нужно.

Пакет [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) предназначен для ASP.NET 4.x, а не для ASP.NET Core.

Дополнительные сведения см. в следующих ресурсах:

* [Общие сведения об Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights для приложений ASP.NET Core](/azure/azure-monitor/app/asp-net-core) — начните изучение с этой статьи, если вы хотите полностью реализовать возможности Application Insights для телеметрии и ведения журналов.
* [ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) (Поставщик ведения журналов Application Insights для журналов .NET Core ILogger) — начните изучение с этой статьи, если вы хотите внедрить поставщика ведения журналов, не используя остальные возможности Application Insights для телеметрии.
* [Адаптеры ведения журналов в Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Инструментирование серверного кода веб-приложения с помощью Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) — интерактивный учебник на сайте Microsoft Learn.

## <a name="third-party-logging-providers"></a>Сторонние поставщики ведения журналов

Некоторые сторонние платформы ведения журналов, которые работают с ASP.NET Core:

* [ELMAH.IO](https://elmah.io/) ([в репозитории GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging));
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([в репозитории GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([в репозитории GitHub](https://github.com/mperdeck/jsnlog));
* [KissLog.net](https://kisslog.net/) ([репозиторий GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([репозиторий GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([в репозитории GitHub](https://github.com/imobile3/Loggr.Extensions.Logging));
* [NLog](https://nlog-project.org/) ([в репозитории GitHub](https://github.com/NLog/NLog.Extensions.Logging));
* [PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([в репозитории GitHub](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))
* [Sentry](https://sentry.io/welcome/) ([репозиторий GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([в репозитории GitHub](https://github.com/serilog/serilog-aspnetcore)).
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([репозиторий Github](https://github.com/googleapis/google-cloud-dotnet))

Некоторые сторонние платформы выполняют [семантическое ведение журналов, также известное как структурированное ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Использование сторонней платформы аналогично использованию одного из встроенных поставщиков:

1. Добавьте пакет NuGet в проект.
1. Вызовите метод расширения `ILoggerFactory`, предоставляемый платформой ведения журналов.

Дополнительные сведения см. в документации по каждому поставщику. Сторонние поставщики ведения журналов не поддерживаются корпорацией Майкрософт.

<a name="nhca"></a>

## <a name="non-host-console-app"></a>Консольные приложения без размещения

Пример использования универсального узла в приложении, не являющемся веб-консолью, см. в файле *Program.cs* [примера приложения "Фоновые задачи"](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).

Код ведения журнала для приложений без универсального узла отличается тем, как [добавляются поставщики](#add-providers) и [создаются средства ведения журнала](#create-logs). 

### <a name="logging-providers"></a>Поставщики ведения журнала

В консольном приложении, не использующем узел, вызовите метод расширения `Add{provider name}` поставщика при создании `LoggerFactory`:

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a>Создание журналов

Чтобы создать журналы, используйте объект <xref:Microsoft.Extensions.Logging.ILogger%601>. Используйте `LoggerFactory`, чтобы создать `ILogger`.

В приведенном ниже примере для создается средство ведения журнала с категорией `LoggingConsoleApp.Program`.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

В приведенных ниже примерах ASP.NET CORE средство ведения журнала используется для создания журналов с уровнем `Information`. *Уровень* ведения журналов определяет степень серьезности или важности записанного в журнал события.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

[Уровни](#log-level) и [категории](#log-category) рассматриваются подробнее в этом документе.

<a name="lhc"></a>

## <a name="log-during-host-construction"></a>Ведение журнала во время создания узла

Ведение журнала во время создания узла не поддерживается напрямую. Однако можно использовать отдельное средство ведения журнала. В следующем примере для входа в `CreateHostBuilder` используется средство ведения журнала [Serilog](https://serilog.net/). `AddSerilog` использует статическую конфигурацию, указанную в `Log.Logger`.

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a>Настройка службы, зависящей от ILogger

Внедрение средства ведения журнала в класс `Startup` посредством конструктора работает в более ранних версиях ASP.NET Core, так как для веб-узла создается отдельный контейнер внедрения зависимостей. Сведения о том, почему для универсального узла создается только один контейнер, см. в [объявлении о критическом изменении](https://github.com/aspnet/Announcements/issues/353).

Чтобы настроить службу, зависящую от `ILogger<T>`, используйте внедрение конструктора или предоставьте фабричный метод. Фабричный метод рекомендуется использовать, только если нет других вариантов. Например, рассмотрим службу, которой требуется экземпляр `ILogger<T>`, предоставляемый путем внедрения зависимостей:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

Выделенный выше код — это функция [Func](/dotnet/api/system.func-2), которая выполняется, когда контейнеру внедрения зависимостей впервые требуется создать экземпляр `MyService`. Таким образом можно обращаться к любым зарегистрированным службам.

<a name="clms"></a>

## <a name="create-logs-in-main"></a>Создание журналов в классе Main

Следующий код создает журналы в `Main`, получая экземпляр `ILogger` путем внедрения зависимостей после создания узла:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a>Создание журналов в классе Startup

Следующий код записывает журналы в `Startup.Configure`:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

Запись журналов до завершения настройки контейнера внедрения зависимостей в методе `Startup.ConfigureServices` не поддерживается:

* Внедрение средства ведения журнала в конструктор `Startup` не поддерживается.
* Внедрение средства ведения журнала в сигнатуру метода `Startup.ConfigureServices` не поддерживается.

Причина этого ограничения заключается в том, что ведение журнала зависит от внедрения зависимостей и от конфигурации, которая, в свою очередь, зависит от внедрения зависимостей. Контейнер внедрения зависимостей не настраивается, пока не завершится выполнение `ConfigureServices`.

Дополнительные сведения о настройке службы, которая зависит от `ILogger<T>`, а также о причинах, по которым внедрение конструктора для средства ведения журнала в `Startup` работало в более ранних версиях, см. в разделе [Настройка службы, зависящей от ILogger](#csdi)

### <a name="no-asynchronous-logger-methods"></a>Асинхронные методы ведения журналов не выполняются

Скорость ведения журналов не должна влиять на производительность выполнения асинхронного кода. Если хранилище данных, предназначенное для регистрации сообщений журнала, работает медленно, сначала записывайте эти сообщения в быстродействующее хранилище, а затем перемещайте их в медленное хранилище. Например, если вы записываете журналы в SQL Server, вам не нужно делать это непосредственно в методе `Log`, так как методы `Log` являются синхронными. Вместо этого синхронно добавьте сообщения журнала в очередь в памяти, и фоновый рабочий поток извлечет сообщения из очереди для выполнения асинхронных операций передачи данных в SQL Server. Дополнительные сведения см. [здесь](https://github.com/dotnet/AspNetCore.Docs/issues/11801) на GitHub.

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a>Изменение уровней ведения журнала в работающем приложении

API ведения журнала не включает сценарий для изменения уровней журнала во время работы приложения. Однако некоторые поставщики конфигурации могут перезагружать конфигурацию, что немедленно влияет на конфигурацию ведения журнала. Например, [поставщик конфигурации файлов](xref:fundamentals/configuration/index#file-configuration-provider) по умолчанию перезагружает конфигурацию ведения журнала. Если конфигурация изменяется в коде во время выполнения приложения, приложение может вызвать [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*), чтобы обновить конфигурацию ведения журнала приложения.

## <a name="ilogger-and-iloggerfactory"></a>ILogger и ILoggerFactory

Интерфейсы <xref:Microsoft.Extensions.Logging.ILogger%601> и <xref:Microsoft.Extensions.Logging.ILoggerFactory>, а также их реализации включены в пакет SDK для .NET Core. Кроме того, они доступны в следующих пакетах NuGet:  

* Эти интерфейсы находятся в пространстве имен [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).
* Их реализации по умолчанию находятся в пакете [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a>Применение правил фильтрации журналов в коде

Для настройки правил фильтрации журналов рекомендуется использовать [конфигурацию](xref:fundamentals/configuration/index).

В следующем примере показано, как зарегистрировать в коде правила фильтрации:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

`logging.AddFilter("System", LogLevel.Debug)` задает категорию `System` и уровень ведения журнала `Debug`. Поскольку конкретный поставщик не задан, фильтр применяется ко всем поставщикам.

`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` задает:

* поставщик ведения журнала `Debug`;
* уровень ведения журнала `Information` и выше;
* все категории, начинающиеся с `"Microsoft"`.

## <a name="create-a-custom-logger"></a>Создание пользовательского средства ведения журнала

Чтобы добавить пользовательское средство ведения журнала, добавьте <xref:Microsoft.Extensions.Logging.ILoggerProvider> с <xref:Microsoft.Extensions.Logging.ILoggerFactory>:

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

`ILoggerProvider` создает один или несколько экземпляров `ILogger`. Экземпляры `ILogger` используются платформой для записи данных в журнал.

### <a name="sample-custom-logger-configuration"></a>Пример конфигурации пользовательского средства ведения журнала

Этот пример выполняет следующее:

* В этом простом примере задается цвет консоли журнала в соответствии с идентификатором события и уровнем ведения журнала. Как правило, средства ведения журнала не изменяются по идентификатору события и не зависят от уровня ведения журнала.
* В этом примере создаются записи консоли разного цвета в соответствии с уровнем ведения журнала и идентификатором события с использованием следующего типа конфигурации:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

Приведенный выше код задает уровень по умолчанию `Warning` и цвет `Yellow`. Если для `EventId` задано значение 0, в журнал записываются все события.

### <a name="create-the-custom-logger"></a>Создание пользовательского средства ведения журнала

В качестве имени категории реализации `ILogger`, как правило, используется источник журнала. Например, тип, в котором создается средство ведения журнала:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

Предыдущий код:

* создает экземпляр средства ведения журнала по имени категории;
* проверяет `logLevel == _config.LogLevel` в `IsEnabled`, благодаря чему каждому `logLevel` соответствует уникальное средство ведения журнала. Как правило, средства ведения журнала также должны быть включены для всех более высоких уровней ведения журнала:

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a>Создание пользовательского LoggerProvider

`LoggerProvider` — это класс, который создает экземпляры средства ведения журнала. Возможно, создавать экземпляры средства ведения журнала для каждой категории не требуется, однако это может быть полезно для некоторых таких средств, например NLog или log4net. Таким образом, при необходимости вы можете выбрать разные целевые объекты вывода журнала для каждой категории:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

В приведенном выше коде <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> создает один экземпляр `ColorConsoleLogger` для каждого имени категории и сохраняет его в [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2).

### <a name="usage-and-registration-of-the-custom-logger"></a>Использование и регистрация пользовательского средства ведения журнала

Средство ведения журнала регистрируется в `Startup.Configure`:

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

В приведенном выше коде необходимо указать по крайней мере один метод расширения для `ILoggerFactory`:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/logging/loggermessage>
* Ошибки, связанные с ведением журналов, следует создавать в репозитории [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues).
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Авторы: [Том Дикстра](https://github.com/tdykstra) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)

.NET Core поддерживает API ведения журналов, который работает с разными встроенными и сторонними поставщиками. В этой статье описано, как использовать в коде API ведения журналов, работающего со встроенными поставщиками.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Добавление поставщиков

Поставщик ведения журналов отображает или сохраняет журналы. Например, поставщик Console отображает журналы на консоли, а поставщик Azure Application Insights может сохранить их в Azure Application Insights. Журналы могут отправляться в несколько назначений после добавления нескольких поставщиков.

Для добавления поставщика вызовите метод расширения `Add{provider name}` поставщика в файле *Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

В указанном выше коде необходимо указать ссылки на `Microsoft.Extensions.Logging` и `Microsoft.Extensions.Configuration`.

Шаблон проекта по умолчанию вызывает метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, который добавляет следующие поставщики ведения журналов:

* Консоль
* Отладка
* EventSource (начиная с версии ASP.NET Core 2.2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Если вы используете `CreateDefaultBuilder`, поставщики по умолчанию можно заменить собственными поставщиками. Вызовите <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> и добавьте требуемые поставщики.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

См. сведения о [встроенных](#built-in-logging-providers) и [сторонних](#third-party-logging-providers) поставщиках ведения журналов.

## <a name="create-logs"></a>Создание журналов

Чтобы создать журналы, используйте объект <xref:Microsoft.Extensions.Logging.ILogger%601>. В веб-приложении или размещенной службе получите `ILogger` посредством внедрения зависимостей (DI). В консольных приложениях без размещения используйте `LoggerFactory` для создания `ILogger`.

В приведенном ниже примере для ASP.NET Core создается средство ведения журнала с категорией `TodoApiSample.Pages.AboutModel`. *Категория* ведения журналов представляет строку, которая связана с каждым журналом. Экземпляр `ILogger<T>`, предоставляемый внедрением зависимостей, создает журналы с полным именем типа `T` в качестве категории. 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

В приведенных ниже примерах ASP.NET Core и консольного приложения средство ведения журнала используется для создания журналов с уровнем `Information`. *Уровень* ведения журналов определяет степень серьезности или важности записанного в журнал события.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[Уровни](#log-level) и [категории](#log-category) рассматриваются подробнее далее в этой статье.

### <a name="create-logs-in-startup"></a>Создание журналов в классе Startup

Для записи журналов в классе `Startup` включите параметр `ILogger` в сигнатуру конструктора:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Создание журналов в классе Program

Для записи журналов в классе `Program` получите экземпляр `ILogger` путем внедрения зависимостей:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

Ведение журнала во время создания узла не поддерживается напрямую. Однако можно использовать отдельное средство ведения журнала. В следующем примере для входа в `CreateWebHostBuilder` используется средство ведения журнала [Serilog](https://serilog.net/). `AddSerilog` использует статическую конфигурацию, указанную в `Log.Logger`.

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a>Асинхронные методы ведения журналов не выполняются

Скорость ведения журналов не должна влиять на производительность выполнения асинхронного кода. Если хранилище данных, предназначенное для регистрации сообщений журнала, работает медленно, сначала записывайте эти сообщения в быстродействующее хранилище, а затем перемещайте их в медленное хранилище. Например, если вы входите в SQL Server, вам не нужно делать это непосредственно в методе `Log`, так как методы `Log` являются синхронными. Вместо этого синхронно добавьте сообщения журнала в очередь в памяти, и фоновый рабочий поток извлечет сообщения из очереди для выполнения асинхронных операций передачи данных в SQL Server. Дополнительные сведения см. [здесь](https://github.com/dotnet/AspNetCore.Docs/issues/11801) на GitHub.

## <a name="configuration"></a>Параметр Configuration

Конфигурацию поставщика ведения журналов предоставляет как минимум один поставщик конфигураций:

* Форматы файлов (INI, JSON и XML).
* аргументы командной строки.
* Переменные среды.
* Объекты .NET в памяти.
* Незашифрованное хранилище [Secret Manager](xref:security/app-secrets) (Диспетчер секретов).
* Зашифрованное пользовательское хранилище, например [Azure Key Vault](xref:security/key-vault-configuration).
* Пользовательские поставщики (установленные или созданные).

Например, конфигурацию ведения журналов обычно предоставляет раздел `Logging` в файле параметров приложения. В следующем примере показано содержимое типичного файла *appsettings.Development.json*.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

Свойство `Logging` может иметь свойство `LogLevel` и свойства поставщика журналов (здесь — Console).

Свойство `LogLevel` в разделе `Logging` указывает минимальный [уровень](#log-level) журнала для выбранных категорий. В этом примере категории `System` и `Microsoft` записываются на уровне `Information`, а остальные — на уровне `Debug`.

Другие свойства в разделе `Logging` определяют поставщиков ведения журналов. Пример приведен для поставщика Console. Если поставщик поддерживает [области журналов](#log-scopes), `IncludeScopes` определяет, включены ли они. Свойство поставщика (например, `Console` в этом примере) также определять свойство `LogLevel`. `LogLevel` под поставщиком указывает уровни журнала для этого поставщика.

Если уровни указаны в `Logging.{providername}.LogLevel`, они не переопределяют ничего из того, что задано в `Logging.LogLevel`. Например, рассмотрим следующий код JSON:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

В приведенном выше коде JSON параметры поставщика `Console` переопределяют предшествующий уровень ведения журнала (по умолчанию).

API ведения журнала не включает сценарий для изменения уровней журнала во время работы приложения. Однако некоторые поставщики конфигурации могут перезагружать конфигурацию, что немедленно влияет на конфигурацию ведения журнала. Например, [Поставщик конфигурации файлов](xref:fundamentals/configuration/index#file-configuration-provider), который добавляется `CreateDefaultBuilder` для чтения файлов параметров, по умолчанию перезагружает конфигурацию ведения журнала. Если конфигурация изменяется в коде во время выполнения приложения, приложение может вызвать [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*), чтобы обновить конфигурацию ведения журнала приложения.

Сведения о реализации поставщиков конфигураций см. в статье <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Пример выходных данных ведения журнала

В примере кода из предыдущего раздела журналы появляются в консоли после запуска приложения из командной строки. Ниже приведен пример выходных данных консоли:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

Предыдущие журналы созданы путем отправки HTTP-запроса Get к примеру приложения по адресу `http://localhost:5000/api/todo/0`.

Ниже приведен пример этих журналов в том виде, в каком они отображаются в окне отладки при запуске примера приложения в Visual Studio.

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Журналы, которые созданы путем вызовов `ILogger`, как показано в предыдущем разделе, начинаются с TodoApi. Журналы, которые начинаются с категорий Microsoft, входят в код платформы ASP.NET Core. В ASP.NET Core и коде приложения используется один и тот же API ведения журналов и одни и те же поставщики.

В оставшейся части этой статьи рассматриваются некоторые сведения и параметры для ведения журналов.

## <a name="nuget-packages"></a>Пакеты NuGet

Интерфейсы `ILogger` и `ILoggerFactory` находятся в [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), а их реализации по умолчанию — в [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Категория журнала

При создании объекта `ILogger` для него указывается *категория*. Эта категория входит в состав каждого сообщения журнала, создаваемого этим экземпляром `ILogger`. Категория может быть любой строкой, обычно используется имя класса, например TodoApi.Controllers.TodoController.

Используйте `ILogger<T>` для получения экземпляра `ILogger`, который использует полное имя типа `T` в качестве категории:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Чтобы явно указать категорию, вызовите `ILoggerFactory.CreateLogger`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

Использование `ILogger<T>` эквивалентно вызову `CreateLogger` с полным именем типа `T`.

## <a name="log-level"></a>Уровень ведения журнала

Каждый журнал задает значение <xref:Microsoft.Extensions.Logging.LogLevel>. Уровень ведения журналов означает степень серьезности или важности. Например, можно создать журнал `Information` при нормальном завершении метода и журнал `Warning`, если метод возвращает код состояния *404 — не найдено*.

Следующий код создает журналы `Information`и `Warning`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

В приведенном выше коде параметры `MyLogEvents.GetItem` и `MyLogEvents.GetItemNotFound` являются [идентификатором события журнала](#log-event-id). Второй параметр — это шаблон сообщения с заполнителями для значений аргументов, предоставляемых оставшимися параметрами метода. Параметры метода рассматриваются более подробно в разделе, посвященном [шаблону сообщений](#lmt) в этой статье.

Методы журналов, которые содержат уровень в имени метода (например, `LogInformation` и `LogWarning`), являются [методами расширения для ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Эти методы вызывают метод `Log`, принимающий параметр `LogLevel`. Метод `Log`, в отличие от этих методов расширения, можно вызывать напрямую, однако его синтаксис довольно сложен. См. сведения о <xref:Microsoft.Extensions.Logging.ILogger> и [исходном коде расширений средства ведения журналов](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

В ASP.NET Core определяются следующие уровни ведения журналов, упорядоченные по возрастанию уровней серьезности.

* Трассировка = 0

  Для получения сведений, которые полезны только при отладке. Эти сообщения могут содержать конфиденциальные данные приложения, поэтому их не следует включать в рабочей среде. *По умолчанию отключено.*

* Отладка = 1

  Для получения сведений, которые полезны при разработке и отладке. Пример. `Entering method Configure with flag set to true.` Включайте уровни ведения журналов `Debug` в рабочей среде только при устранении неполадок, так как такие журналы занимают много места.

* Информация = 2

  Для отслеживания общего потока работы приложения. Эти журналы обычно полезны в долгосрочной перспективе. Пример: `Request received for path /api/todo`

* Предупреждение = 3

  Для нестандартных или непредвиденных событий, возникающих в потоке работы приложения. Это могут быть ошибки или другие условия, которые не приводят к остановке приложения, но которые нужно изучить. Обработанные исключения являются распространенным условием использования уровня ведения журнала `Warning`. Пример: `FileNotFoundException for file quotes.txt.`

* Ошибка = 4

  Для ошибок и исключений, которые не могут быть обработаны. Эти сообщения указывают на сбой текущего действия или операции (например, текущего HTTP-запроса), а не на ошибку уровня приложения. Пример сообщения журнала: `Cannot insert record due to duplicate key violation.`

* Критический = 5

  Для сбоев, которые требуют немедленного внимания. Примеры: потеря данных, нехватка места на диске.

Уровень ведения журналов можно использовать для управления объемом выходных данных, записываемых на определенный носитель или в окно отображения. Пример:

* В рабочей среде:
  * При ведении журнала на уровнях с `Trace` по `Information` создается большой объем подробных сообщений журнала. Чтобы контролировать затраты и не превысить лимиты объема хранилища данных, записывайте сообщения на уровнях с `Trace` по `Information` в хранилище данных с низкими затратами и большим объемом.
  * Ведение журнала на уровнях с `Warning` по `Critical` обычно приводит к записи меньшего количества сообщений меньшего размера. Следовательно, затраты и лимиты хранилища не становятся проблемой, что приводит к большей гибкости при выборе хранилища данных.
* Во время разработки:
  * Записывайте сообщения уровней с `Warning` по `Critical` на консоль.
  * Добавляйте сообщения уровней с `Trace` по `Information` при устранении неполадок.

В разделе [Фильтрация журналов](#log-filtering) далее в этой статье приводятся сведения о том, как контролировать уровни журнала, обрабатываемые поставщиком.

ASP.NET Core создает журналы для событий платформы. В примерах журнала ранее в этой статье не указывались журналы с уровнем ниже `Information`, поэтому журналы уровня `Debug` или `Trace` не создавались. Ниже приведен пример журналов консоли, которые созданы при запуске примера приложения, настроенного на отображение журналов `Debug`:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>Идентификатор события журнала

Каждый журнал может указывать *идентификатор события*. В примере приложения для этого используется локально определенный класс `LoggingEvents`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Идентификатор события связывает набор событий. Например, все журналы, связанные с отображением списка элементов на странице, могут иметь значение 1001.

Поставщик ведения журналов может хранить идентификатор события в поле идентификатора, в сообщении журнала, или вообще не хранить. Поставщик Debug не отображает идентификаторы событий. Поставщик Console отображает идентификаторы событий в квадратных скобках после категории:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Шаблон сообщения журнала

Каждый журнал указывает шаблон сообщения. Шаблон сообщения может содержать заполнители, для которых предоставляются аргументы. Используйте для заполнителей имена, а не числа.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Параметры, используемые для предоставления значений, определяются порядком заполнителей, а не их именами. В приведенном ниже коде обратите внимание на то, что имена параметров идут не по порядку в шаблоне сообщения:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Этот код создает сообщение журнала со значениями параметров в определенном порядке:

```text
Parameter values: parm1, parm2
```

Платформа ведения журналов поддерживает такое поведение, чтобы поставщики ведения журнала могли реализовывать [семантическое ведение журналов, также известное как структурированное ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Сами аргументы передаются в систему ведения журналов, а не только в отформатированный шаблон сообщения. Эта информация позволяет поставщикам ведения журналов хранить значения параметров как поля. Предположим, например, что вызовы метода средства ведения журналов выглядят так:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Если вы отправляете журналы в Хранилище таблиц Azure, каждая сущность таблицы Azure может иметь свойства `ID` и `RequestTime`, что упрощает выполнение запросов к данным журналов. Запрос может находить все журналы в пределах определенного диапазона `RequestTime`, не анализируя время ожидания текстового сообщения.

## <a name="logging-exceptions"></a>Ведение журнала исключений

Методы средства ведения журнала имеют перегрузки, которые позволяют передавать исключение, как показано в следующем примере:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Разные поставщики обрабатывают сведения об исключениях по-разному. Ниже приведен пример выходных данных поставщика Debug из приведенного выше кода.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Фильтрация журналов

Можно указать минимальный уровень ведения журнала для определенного поставщика и категории или для всех поставщиков или всех категорий. Все журналы с уровнем ниже минимального не передаются этому поставщику, поэтому они не отображаются и не сохраняются.

Чтобы проигнорировать все журналы, укажите `LogLevel.None` в качестве минимального уровня ведения журналов. `LogLevel.None` равно целочисленному значению 6, то есть больше, чем `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Создание правил фильтрации в конфигурации

Код шаблона проектов вызывает `CreateDefaultBuilder` для настройки ведения журналов для поставщиков Console, Debug и EventSource (ASP.NET Core 2.2 или более поздняя версия). Метод `CreateDefaultBuilder` настраивает ведение журнала для просмотра конфигурации в разделе `Logging`, как было описано [ранее в этой статье](#configuration).

Данные конфигурации указывают минимальные уровни ведения журнала для каждого поставщика и категории, как показано в следующем примере:

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Этот JSON создает шесть правил фильтрации: одно для поставщика Debug, четыре для поставщика Console и одно для всех поставщиков. При создании объекта `ILogger` для каждого поставщика выбирается только одно из этих правил.

### <a name="filter-rules-in-code"></a>Правила фильтрации в коде

В следующем примере показано, как зарегистрировать в коде правила фильтрации:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

Второй `AddFilter` указывает поставщика, Debug, используя имя его типа. Первый `AddFilter` применяется ко всем поставщикам, так как он не определяет тип поставщика.

### <a name="how-filtering-rules-are-applied"></a>Применение правил фильтрации

Данные конфигурации и код `AddFilter`, приведенный в предыдущих примерах, создают правила, показанные в следующей таблице. Первые шесть взяты из примера конфигурации, а последние два — из примера кода.

| Число | Поставщик      | Категории, которые начинаются с...          | Минимальный уровень ведения журнала |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Отладка         | Все категории                          | Сведения       |
| 2      | Консоль       | Microsoft.AspNetCore.Mvc.Razor.Internal | Предупреждение           |
| 3      | Консоль       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Отладка             |
| 4      | Консоль       | Microsoft.AspNetCore.Mvc.Razor          | Ошибка             |
| 5      | Консоль       | Все категории                          | Сведения       |
| 6      | Все поставщики | Все категории                          | Отладка             |
| 7      | Все поставщики | Система                                  | Отладка             |
| 8      | Отладка         | Майкрософт                               | Трассировка             |

При создании объекта `ILogger` объект `ILoggerFactory` выбирает одно правило для каждого поставщика, которое будет применено к этому средству ведения журналов. Все сообщения, записываемые с помощью экземпляра `ILogger`, фильтруются на основе выбранных правил. Самое подходящее правило для каждой пары поставщика и категории выбирается из списка доступных правил.

При создании `ILogger` для данной категории для каждого поставщика используется приведенный далее алгоритм:

* Выберите все правила, которые соответствуют поставщику или его псевдониму. Если ничего не найдено, выберите все правила с пустым поставщиком.
* В результатах предыдущего шага выберите правила с самым длинным соответствующим префиксом категории. Если ничего не найдено, выберите все правила, которые не указывают категорию.
* Если выбрано несколько правил, примите **последнее**.
* Если правила не выбраны, используйте `MinimumLevel`.

Предположим, у вас есть указанный выше список правил и вы создаете объект `ILogger` для категории Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine:

* К поставщику Debug применяются правила 1, 6 и 8. Правило 8 является наиболее подходящим, поэтому выбрано оно.
* К поставщику отладки применяются правила 3, 4, 5 и 6. Правило 3 является наиболее подходящим.

Полученный в результате экземпляр `ILogger` отправляет журналы уровня `Trace` и выше в поставщик Debug. Журналы уровня `Debug` и выше отправляются в поставщик Console.

### <a name="provider-aliases"></a>Псевдонимы поставщиков

Каждый поставщик определяет *псевдоним*, используемый в конфигурации вместо полного имени типа.  Для встроенных поставщиков используйте следующие псевдонимы:

* Консоль
* Отладка
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Минимальный уровень по умолчанию

Существует параметр минимального уровня, который применяется, только если к определенному поставщику или категории не подходят правила из конфигурации или кода. В следующем примере показано, как задать минимальный уровень:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Если минимальный уровень не задан явным образом, используется значение по умолчанию — `Information`, означающее, что журналы `Trace` и `Debug` игнорируются.

### <a name="filter-functions"></a>Функции фильтрации

Функция фильтрации вызывается для всех поставщиков и категорий, которым в конфигурации и (или) коде не назначены правила. Код в функции имеет доступ к типу поставщика, категории и уровню ведения журналов. Пример:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a>Системные категории и уровни

Ниже приведены некоторые категории, используемые ASP.NET Core и Entity Framework Core с заметками о журналах:

| Категория                            | Примечания |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Общая диагностика ASP.NET Core. |
| Microsoft.AspNetCore.DataProtection | Распознанные, найденные и использованные ключи. |
| Microsoft.AspNetCore.HostFiltering  | Разрешенные узлы. |
| Microsoft.AspNetCore.Hosting        | Время начала и длительность выполнения HTTP-запросов. Определение загруженных начальных сборок размещения. |
| Microsoft.AspNetCore.Mvc            | Диагностика MVC и Razor. Привязка моделей, выполнение фильтра, компиляция представлений, выбор действия. |
| Microsoft.AspNetCore.Routing        | Перенаправление соответствующих сведений. |
| Microsoft.AspNetCore.Server         | Запуск, остановка и сохранение ответов. Сведения о сертификате HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | Обработанные файлы. |
| Microsoft.EntityFrameworkCore       | Общая диагностика Entity Framework Core. Действия и конфигурация базы данных, обнаружение изменений, переносы. |

## <a name="log-scopes"></a>Области журналов

 *Область* может группировать набор логических операций. Эту возможность можно использовать для присоединения одних и тех же данных к каждому журналу, созданному как часть набора. Например, каждый журнал, созданный в ходе обработки транзакции, может включать идентификатор транзакции.

Область — это тип `IDisposable`, возвращаемый методом <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> и действующий до удаления. Используйте область, заключив вызовы средства ведения журналов в блок `using`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Следующий код предоставляет области для поставщика Console:

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> Для включения ведения журнала на уровне области требуется параметр `IncludeScopes` средства ведения журналов.
>
> Сведения о настройках см. в разделе [Конфигурация](#configuration).

Каждое сообщение журнала содержит ограниченную информацию:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Встроенные поставщики ведения журналов

В состав ASP.NET Core входят следующие поставщики:

* [Консоль](#console-provider)
* [Отладка](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Сведения о stdout и ведении журнала отладки с помощью модуля ASP.NET Core см. в статьях <xref:test/troubleshoot-azure-iis> и <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console-provider"></a>Поставщик Console

Пакет поставщика [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) отправляет выходные данные журнала на консоль. 

```csharp
logging.AddConsole();
```

Чтобы просмотреть выходные данные ведения журналов в консоли, откройте командную строку, перейдите в папку проекта и запустите приведенную ниже команду:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Поставщик Debug

Пакет поставщика [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) записывает выходные данные журналов с помощью класса [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (вызовов метода `Debug.WriteLine`).

В Linux этот поставщик записывает журналы в каталог */var/log/message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Поставщик источника событий

Пакет поставщика [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) записывает данные в источник событий на кросс-платформенный сервер с именем `Microsoft-Extensions-Logging`. В Windows поставщик использует [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

Поставщик источника событий добавляется автоматически при вызове `CreateDefaultBuilder` для сборки узла.

Для сбора и просмотра данных журналов рекомендуется использовать [программу PerfView](https://github.com/Microsoft/perfview). Существуют и другие средства для просмотра журналов трассировки событий Windows, но PerfView обеспечивает максимальное удобство работы с событиями трассировки событий Windows, создаваемыми ASP.NET Core.

Чтобы настроить PerfView для сбора событий, регистрируемых этим поставщиком, добавьте строку `*Microsoft-Extensions-Logging` в список **Дополнительные поставщики**. (Не пропустите звездочку в начале строки.)

![Дополнительные поставщики Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Поставщик Windows EventLog

Пакет поставщика [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) отправляет выходные данные журнала в журнал событий Windows.

```csharp
logging.AddEventLog();
```

[Перегрузки AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) позволяют передавать <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>. Если `null` или не указан, используются следующие параметры по умолчанию:

* `LogName`. "Application"
* `SourceName`: ".NET Runtime"
* `MachineName`. Используется имя локального компьютера.

События регистрируются для [уровня предупреждения и более высоких уровней](#log-level). В следующем примере для журнала событий по умолчанию задается уровень <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a>Поставщик TraceSource

Пакет поставщика [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) использует библиотеки и поставщики <xref:System.Diagnostics.TraceSource>.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[Перегрузки AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) позволяют передавать исходный параметр и прослушиватель трассировки.

Чтобы использовать этот поставщик, приложение должно выполняться в .NET Framework (а не в .NET Core). Поставщик позволяет перенаправлять сообщения различным [прослушивателям](/dotnet/framework/debug-trace-profile/trace-listeners), например <xref:System.Diagnostics.TextWriterTraceListener>, который используется в примере приложения.

### <a name="azure-app-service-provider"></a>Поставщик службы приложений Azure

Пакет поставщика [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) записывает журналы в текстовые файлы в файловой системе приложения службы приложений Azure и в [хранилище больших двоичных объектов](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) в учетной записи хранения Azure.

```csharp
logging.AddAzureWebAppDiagnostics();
```

Этот пакет не входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Если планируется использовать .NET Framework или будет указана ссылка на метапакет `Microsoft.AspNetCore.App`, добавьте пакет поставщика в проект. 

Перегрузка <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> позволяет передавать <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. Объект параметров может переопределять параметры по умолчанию, например шаблон выходных данных ведения журналов, имя BLOB-объекта и максимально допустимый размер файла. (*Шаблон выходных данных* — это шаблон сообщений, который применяется ко всем журналам наряду с тем, что предоставляется при вызове метода `ILogger`.)

При развертывании в приложение Службы приложений ваше приложение использует параметры в разделе [Журналы Службы приложений](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) на странице **Служба приложений** на портале Azure. При обновлении следующих параметров изменения вступают в силу немедленно без перезапуска или повторного развертывания приложения:

* **Ведение журнала приложения (файловая система)** ;
* **Ведение журнала приложения (BLOB-объект)** .

По умолчанию файлы журнала находятся в папке *D:\\home\\LogFiles\\Application*, а имя файла по умолчанию — *diagnostics-yyyymmdd.txt*. Максимальный размер файла по умолчанию составляет 10 МБ, а максимальное количество сохраняемых по умолчанию файлов равно 2. Имя BLOB-объекта по умолчанию — *{имя_приложения}{метка_времени}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.

Поставщик работает только при выполнении проекта в среде Azure. Он не функционирует при запуске проекта в локальной среде &mdash;(то есть не выполняет запись в локальные файлы или в локальное хранилище разработки для больших двоичных объектов).

#### <a name="azure-log-streaming"></a>Потоковая передача журналов Azure

Потоковая передача журналов Azure позволяет просматривать активность журнала в реальном времени из следующих источников:

* сервер приложений;
* веб-сервер;
* трассировка неудачно завершенных запросов.

Настройка потоковой передачи журналов Azure

* Со страницы портала приложения перейдите на страницу **Журналы Службы приложений**.
* **Включите** параметр **Ведение журнала приложения (файловая система)** .
* Выберите **уровень** ведения журнала. Этот параметр применяется только к потоковой передаче журналов Azure, а не к другим поставщикам ведения журнала в приложении.

Перейдите на страницу **Поток журналов**, чтобы просмотреть сообщения приложения. Они записываются в журнал приложением через интерфейс `ILogger`.

### <a name="azure-application-insights-trace-logging"></a>Ведение журнала трассировки Azure Application Insights

Пакет поставщика [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) записывает журналы в Azure Application Insights. Служба Application Insights отслеживает веб-приложения и предоставляет средства для создания запросов и анализа данных телеметрии. Используя этого поставщика, вы сможете выполнять запросы к журналам и их анализ с помощью средств Application Insights.

Поставщик ведения журнала включается как зависимость [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Этот пакет предоставляет всю доступную телеметрию для ASP.NET Core. Если вы используете этот пакет, пакет поставщика устанавливать не нужно.

Не используйте пакет [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web), который предназначен для ASP.NET 4.x.

Дополнительные сведения см. в следующих ресурсах:

* [Общие сведения об Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights для приложений ASP.NET Core](/azure/azure-monitor/app/asp-net-core) — начните изучение с этой статьи, если вы хотите полностью реализовать возможности Application Insights для телеметрии и ведения журналов.
* [ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) (Поставщик ведения журналов Application Insights для журналов .NET Core ILogger) — начните изучение с этой статьи, если вы хотите внедрить поставщика ведения журналов, не используя остальные возможности Application Insights для телеметрии.
* [Адаптеры ведения журналов в Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Инструментирование серверного кода веб-приложения с помощью Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) — интерактивный учебник на сайте Microsoft Learn.

## <a name="third-party-logging-providers"></a>Сторонние поставщики ведения журналов

Некоторые сторонние платформы ведения журналов, которые работают с ASP.NET Core:

* [ELMAH.IO](https://elmah.io/) ([в репозитории GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging));
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([в репозитории GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([в репозитории GitHub](https://github.com/mperdeck/jsnlog));
* [KissLog.net](https://kisslog.net/) ([репозиторий GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([репозиторий GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([в репозитории GitHub](https://github.com/imobile3/Loggr.Extensions.Logging));
* [NLog](https://nlog-project.org/) ([в репозитории GitHub](https://github.com/NLog/NLog.Extensions.Logging));
* [Sentry](https://sentry.io/welcome/) ([репозиторий GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([в репозитории GitHub](https://github.com/serilog/serilog-aspnetcore)).
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([репозиторий Github](https://github.com/googleapis/google-cloud-dotnet))

Некоторые сторонние платформы выполняют [семантическое ведение журналов, также известное как структурированное ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Использование сторонней платформы аналогично использованию одного из встроенных поставщиков:

1. Добавьте пакет NuGet в проект.
1. Вызовите метод расширения `ILoggerFactory`, предоставляемый платформой ведения журналов.

Дополнительные сведения см. в документации по каждому поставщику. Сторонние поставщики ведения журналов не поддерживаются корпорацией Майкрософт.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
