---
title: Введение в ASP.NET Core
author: rick-anderson
description: Вводные сведения об ASP.NET Core, кроссплатформенной высокопроизводительной платформе с открытым исходным кодом для создания современных облачных интернет-приложений.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: index
ms.openlocfilehash: f1235337748bacdb6eaa775a5853238bf729e605
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634557"
---
# <a name="introduction-to-aspnet-core"></a>Введение в ASP.NET Core

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27), [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT) и [Шон Луттин (Shaun Luttin)](https://twitter.com/dicshaunary)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core является кроссплатформенной, высокопроизводительной средой с [открытым исходным кодом](https://github.com/dotnet/aspnetcore) для создания современных облачных приложений, подключенных к Интернету. ASP.NET Core позволяет выполнять следующие задачи:

* Создавать веб-приложения и службы, приложения [Интернета вещей](https://www.microsoft.com/internet-of-things/) (IoT) и серверные части для мобильных приложений.
* Использовать избранные средства разработки в Windows, macOS и Linux.
* Выполнять развертывания в облаке или локальной среде.
* Запускать в [.NET Core](/dotnet/core/introduction).

## <a name="why-choose-aspnet-core"></a>Преимущества, обеспечиваемые ASP.NET Core

Миллионы разработчиков использовали и продолжают использовать [ASP.NET 4.x](/aspnet/overview) для создания веб-приложений. ASP.NET Core — это модификация ASP.NET 4.x с архитектурными изменениями, формирующими более рациональную и более модульную платформу.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Создание веб-API и пользовательского веб-интерфейса с помощью ASP.NET Core MVC

ASP.NET Core MVC предоставляет функции, которые позволяют создавать [веб-интерфейсы API](xref:tutorials/first-web-api) и [веб-приложения](xref:tutorials/razor-pages/index).

* [Шаблон Model-View-Controller (MVC)](xref:mvc/overview) помогает сделать веб-API и веб-приложения тестируемыми.
* [Razor Pages](xref:razor-pages/index) — это основанная на страницах модель программирования, которая упрощает и повышает эффективность создания пользовательского веб-интерфейса.
* [Разметка Razor](xref:mvc/views/razor) предоставляет эффективный синтаксис для страниц [Razor Pages](xref:razor-pages/index) и [представлений MVC](xref:mvc/views/overview).
* [Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.
* Благодаря встроенной поддержке [нескольких форматов данных и согласованию содержимого](xref:web-api/advanced/formatting) веб-API становятся доступными для множества клиентов, включая браузеры и мобильные устройства.
* [Привязка модели](xref:mvc/models/model-binding) автоматически сопоставляет данные из HTTP-запросов с параметрами методов действия.
* [Проверка модели](xref:mvc/models/validation) автоматически выполняется на стороне сервера и клиента.

## <a name="client-side-development"></a>Клиентская разработка

ASP.NET Core легко интегрируется с распространенными клиентскими платформами и библиотеками, в том числе [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) и [Bootstrap](https://getbootstrap.com/). Подробнее см. <xref:blazor/index> и связанные материалы о *разработке на стороне клиента*.

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>Целевые версии платформы ASP.NET Core

ASP.NET Core 3.x и более поздние версии может выполняться только в .NET Core. Как правило, ASP.NET Core состоит из библиотек [.NET Standard](/dotnet/standard/net-standard). Библиотеки, написанные на .NET Standard 2.0 под управлением любой [платформы .NET с реализацией .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

При использовании .NET Core существуют некоторые преимущества, и их число увеличивается с каждым выпуском. Преимущества .NET Core по сравнению с .NET Framework включают:

* Кроссплатформенность. Выполняется в Windows, macOS и Linux.
* Повышение производительности
* [Управление параллельными версиями](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Новые интерфейсы API
* Открытый код

## <a name="recommended-learning-path"></a>Рекомендуемая схема обучения

Для знакомства с разработкой приложений ASP.NET Core рекомендуется изучить следующую последовательность учебников.

1. Пройдите учебник по тому типу приложения, которое вы собираетесь разрабатывать или обслуживать.

   |Тип приложения  |Сценарий  |Учебник  |
   |----------|----------|----------|
   |Веб-приложение                   | Разработка нового веб-интерфейса на стороне сервера |[Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start) |
   |Веб-приложение                   | Обслуживание приложения MVC |[Начало работы с MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Веб-приложение                   | Разработка веб-интерфейса на стороне клиента |[Начало работы с Blazor](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |Веб-интерфейс API                   | Службы HTTP RESTFUL |[Создание веб-API](xref:tutorials/first-web-api)&dagger; |
   |Приложение удаленного вызова процедур | Разработка в соответствии с парадигмой "Сначала контракт" с использованием Protocol Buffers |[Начало работы со службой gRPC](xref:tutorials/grpc/grpc-start) |
   |Приложение режима реального времени             | Двунаправленный обмен данными между сервером и подключенными к нему клиентами |[Начало работы с SignalR](xref:tutorials/signalr) |

1. Пройдите учебник, посвященный основам доступа к данным.

   |Сценарий  |Учебник  |
   |----------|----------|
   |Разработка нового приложения        |[Razor Pages с Entity Framework Core](xref:data/ef-rp/intro) |
   |Обслуживание приложения MVC |[MVC с Entity Framework Core](xref:data/ef-mvc/intro) |

1. Ознакомьтесь с обзором [основ](xref:fundamentals/index) ASP.NET Core, относящихся ко всем типам приложений.

1. Просмотрите содержание, чтобы найти другие интересующие вас темы.

&dagger;Доступен [интерактивный учебник по веб-API](/learn/modules/build-web-api-net-core). Локальная установка средств разработки не требуется. Код выполняется в [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) в браузере, а для тестирования используется [curl](https://curl.haxx.se/).

## <a name="migrate-from-net-framework"></a>Миграция с .NET Framework

Справочное руководство по миграции приложений ASP.NET 4.x на ASP.NET Core см. в статье <xref:migration/proper-to-2x/index>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core является кроссплатформенной, высокопроизводительной средой с [открытым исходным кодом](https://github.com/dotnet/aspnetcore) для создания современных облачных приложений, подключенных к Интернету. ASP.NET Core позволяет выполнять следующие задачи:

* Создавать веб-приложения и службы, приложения [Интернета вещей](https://www.microsoft.com/internet-of-things/) (IoT) и серверные части для мобильных приложений.
* Использовать избранные средства разработки в Windows, macOS и Linux.
* Выполнять развертывания в облаке или локальной среде.
* Работать в [.NET Core или .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="why-choose-aspnet-core"></a>Преимущества, обеспечиваемые ASP.NET Core

Миллионы разработчиков использовали и продолжают использовать [ASP.NET 4.x](/aspnet/overview) для создания веб-приложений. ASP.NET Core — это модификация ASP.NET 4.x с архитектурными изменениями, формирующими более рациональную и более модульную платформу.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Создание веб-API и пользовательского веб-интерфейса с помощью ASP.NET Core MVC

ASP.NET Core MVC предоставляет функции, которые позволяют создавать [веб-интерфейсы API](xref:tutorials/first-web-api) и [веб-приложения](xref:tutorials/razor-pages/index).

* [Шаблон Model-View-Controller (MVC)](xref:mvc/overview) помогает сделать веб-API и веб-приложения тестируемыми.
* [Razor Pages](xref:razor-pages/index) — это основанная на страницах модель программирования, которая упрощает и повышает эффективность создания пользовательского веб-интерфейса.
* [Разметка Razor](xref:mvc/views/razor) предоставляет эффективный синтаксис для страниц [Razor Pages](xref:razor-pages/index) и [представлений MVC](xref:mvc/views/overview).
* [Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.
* Благодаря встроенной поддержке [нескольких форматов данных и согласованию содержимого](xref:web-api/advanced/formatting) веб-API становятся доступными для множества клиентов, включая браузеры и мобильные устройства.
* [Привязка модели](xref:mvc/models/model-binding) автоматически сопоставляет данные из HTTP-запросов с параметрами методов действия.
* [Проверка модели](xref:mvc/models/validation) автоматически выполняется на стороне сервера и клиента.

## <a name="client-side-development"></a>Клиентская разработка

ASP.NET Core легко интегрируется с распространенными клиентскими платформами и библиотеками, в том числе [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) и [Bootstrap](https://getbootstrap.com/). Подробнее см. <xref:blazor/index> и связанные материалы о *разработке на стороне клиента*.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>ASP.NET Core для платформы .NET Framework

Приложения ASP.NET Core 2.x могут выполняться в .NET Core или .NET Framework. Приложения ASP.NET Core, предназначенные для .NET Framework, не являются кроссплатформенными &mdash; они выполняются только в Windows. Как правило, ASP.NET Core 2.x состоит из библиотек [.NET Standard](/dotnet/standard/net-standard). Библиотеки, написанные на .NET Standard 2.0 под управлением любой [платформы .NET с реализацией .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

ASP.NET Core 2.x поддерживается в версиях .NET Framework с реализацией .NET Standard 2.0:

* Рекомендуется использовать последнюю версию .NET Framework.
* .NET Framework 4.6.1 и более поздних версий.

ASP.NET Core версии 3.0 и более поздних будут выполняться только в .NET Core. Дополнительные сведения об этом изменении см. в разделе [Первое знакомство с предстоящими изменениями в ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

При использовании .NET Core существуют некоторые преимущества, и их число увеличивается с каждым выпуском. Преимущества .NET Core по сравнению с .NET Framework включают:

* Кроссплатформенность. Выполняется на macOS, Linux и Windows.
* Повышение производительности
* [Управление параллельными версиями](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Новые интерфейсы API
* Открытый код

Благодаря [пакету обеспечения совместимости Windows](/dotnet/core/porting/windows-compat-pack) в .NET Core доступны тысячи API-интерфейсов, созданных только для Windows, что позволяет расширить диапазон API при переходе с .NET Framework на .NET Core. Эти API-интерфейсы не были доступны в .NET Core 1.x.

## <a name="recommended-learning-path"></a>Рекомендуемая схема обучения

Для знакомства с разработкой приложений ASP.NET Core рекомендуется изучить следующую последовательность учебников и статей.

1. Пройдите учебник по тому типу приложения, которое вы собираетесь разрабатывать или обслуживать.

   |Тип приложения  |Сценарий  |Учебник  |
   |----------|----------|----------|
   |Веб-приложение                   | Разработка нового приложения        |[Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start) |
   |Веб-приложение                   | Обслуживание приложения MVC |[Начало работы с MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Веб-интерфейс API                   |                            |[Создание веб-API](xref:tutorials/first-web-api)&dagger; |
   |Приложение режима реального времени             |                            |[Начало работы с SignalR](xref:tutorials/signalr) |

1. Пройдите учебник, посвященный основам доступа к данным.

   |Сценарий  |Учебник  |
   |----------|----------|
   | Разработка нового приложения        |[Razor Pages с Entity Framework Core](xref:data/ef-rp/intro) |
   | Обслуживание приложения MVC |[MVC с Entity Framework Core](xref:data/ef-mvc/intro) |

1. Ознакомьтесь с обзором [основ](xref:fundamentals/index) ASP.NET Core, относящихся ко всем типам приложений.

1. Просмотрите содержание, чтобы найти другие интересующие вас темы.

&dagger; Доступен новый [учебник по веб-API с прохождением в браузере](/learn/modules/build-web-api-net-core), не требующий установки локальной интегрированной среды разработки. Код выполняется в [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), а для тестирования используется [curl](https://curl.haxx.se/).

## <a name="migrate-from-net-framework"></a>Миграция с .NET Framework

Справочное руководство по миграции приложений ASP.NET на ASP.NET Core см. в статье <xref:migration/proper-to-2x/index>.

::: moniker-end

## <a name="how-to-download-a-sample"></a>Загрузка примера

Многие статьи и учебники содержат ссылки на примеры кода.

1. [Загрузите ZIP-файл репозитория ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).
1. Распакуйте файл *Docs-master.zip*.
1. Перейдите в папку примера по URL-адресу, указанному в примере.

### <a name="preprocessor-directives-in-sample-code"></a>Директивы препроцессора в примере кода

Для демонстрации нескольких сценариев в примерах приложений используются директивы препроцессора `#define` и `#if-#else/#elif-#endif`, выборочно компилирующие и запускающие разные фрагменты примеров кода. В примерах, где применяется этот подход, задайте в начале файлов C# директиву `#define` для определения символа, связанного со сценарием, который нужно запустить. Для запуска сценария в некоторых примерах потребуется определить символ в начале нескольких файлов.

Например, в следующем списке символов `#define` видно, что доступно четыре сценария (один сценарий на символ). В текущем примере конфигурации запускается сценарий `TemplateCode`:

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

Чтобы запустить в примере сценарий `ExpandDefault`, задайте символ `ExpandDefault` и оставьте остальные символы раскомментированными:

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

Дополнительные сведения об использовании [директив препроцессора C#](/dotnet/csharp/language-reference/preprocessor-directives/) для выборочной компиляции фрагментов кода см. в разделах [#define (Справочник по C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) и [#if (Справочник по C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).

### <a name="regions-in-sample-code"></a>Регионы в примере кода

Некоторые примеры приложений содержат фрагменты кода внутри директив C# [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) и [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion). Система сборки документации вставляет эти регионы в обработанные разделы документации.  

Названия регионов обычно содержат слово "фрагмент". В следующем примере показан регион с именем `snippet_WebHostDefaults`:

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

На предыдущий фрагмент кода C# указывает ссылка в следующей строке в файле Markdown раздела:

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

Вы можете спокойно проигнорировать или удалить директивы `#region` и `#endregion` вокруг кода. Не изменяйте код внутри этих директив, если планируете запустить примеры сценариев, описанные в разделе. Вы можете изменить код, экспериментируя с другими сценариями.

Дополнительные сведения см. в разделе[Участие в написании документации ASP.NET: Фрагменты кода](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения см. в следующих ресурсах:

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Основы ASP.NET Core](xref:fundamentals/index)
* [В еженедельном выпуске ASP.NET Community Standup](https://live.asp.net/) рассматривается ход работы и планы команды. Помимо этого, публикуются новые блоги и стороннее программное обеспечение.
