---
title: Миграция с ASP.NET Core 1.x на 2.0
author: scottaddie
description: В этой статье описываются предварительные требования и стандартные этапы миграции проекта ASP.NET Core 1.x в ASP.NET Core 2.0.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/index
ms.openlocfilehash: 1b7b89b130f66c851bf01d0eb6d643e4b3676a1e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774227"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a>Миграция с ASP.NET Core 1.x на 2.0

Автор: [Скотт Адди](https://github.com/scottaddie) (Scott Addie)

В этой статье поэтапно рассматривается обновление существующего проекта ASP.NET Core 1.x до версии ASP.NET Core 2.0. Миграция приложения в ASP.NET Core 2.0 позволяет воспользоваться [множеством новых функций и улучшений производительности](xref:aspnetcore-2.0).

Существующие приложения ASP.NET Core 1.x основаны на шаблонах проектов для определенной версии. По мере развития платформы ASP.NET Core совершенствуются шаблоны проектов и содержащийся в них начальный код. Помимо обновления платформы ASP.NET Core, необходимо также обновить код приложения.

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Предварительные требования

См. [Начало работы с ASP.NET Core](xref:getting-started).

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>Обновление моникера целевой платформы (TFM)

Проекты, предназначенные для .NET Core, должны использовать [моникер целевой платформы](/dotnet/standard/frameworks) версии не ниже .NET Core 2.0. Найдите в файле *CSPROJ* узел `<TargetFramework>` и замените его содержимое на `netcoreapp2.0`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

Проекты, предназначенные для .NET Framework, должны использовать моникер целевой платформы версии не ниже .NET Framework 4.6.1. Найдите в файле *CSPROJ* узел `<TargetFramework>` и замените его содержимое на `net461`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> .NET Core 2.0 обеспечивает гораздо большую контактную зону по сравнению с .NET Core 1.x. Если вы используете .NET Framework только из-за отсутствия нужных API в .NET Core 1.x, скорее всего, .NET Core 2.0 удовлетворит ваши потребности.

Если файл проекта содержит `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, см. [эту проблему на GitHub](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>Обновление версии пакета SDK для .NET Core в файле global.json

Если ваше решение использует файл [global.json](/dotnet/core/tools/global-json) для указания целевой версии пакета SDK для .NET Core, измените значение свойства `version` так, чтобы использовалась версия 2.0, установленная на компьютере:

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>Обновление ссылок на пакеты

В файле *CSPROJ* в проекте версии 1.x перечислены все проекты NuGet, используемые проектом.

В проекте ASP.NET Core 2.0, предназначенном для .NET Core 2.0, коллекция пакетов в файле *CSPROJ* заменяется ссылкой на один [метапакет](xref:fundamentals/metapackage):

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

В метапакет входят все компоненты ASP.NET Core 2.0 и Entity Framework Core 2.0.

В проектах ASP.NET Core 2.0, предназначенных для .NET Framework, по-прежнему должны использоваться ссылки на отдельные пакеты NuGet. Измените значение атрибута `Version` каждого узла `<PackageReference />` на 2.0.0.

Например, вот список узлов `<PackageReference />`, используемых в типичном проекте ASP.NET Core 2.0, предназначенном для .NET Framework:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>Обновление средств CLI для .NET Core

Измените значение атрибута *каждого узла* в файле `Version`CSPROJ`<DotNetCliToolReference />` на 2.0.0.

Например, вот список средств CLI, используемых в типичном проекте ASP.NET Core 2.0, предназначенном для .NET Core 2.0:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>Переименование свойства PackageTargetFallback

В проектах версии 1.x в файлах *CSPROJ* использовался узел `PackageTargetFallback` и переменная:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

Переименуйте этот узел и переменную в `AssetTargetFallback`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>Обновление метода Main в файле Program.cs

В проектах версии 1.x метод `Main` в файле *Program.cs* выглядел так:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

В проектах версии 2.0 метод `Main` в файле *Program.cs* упрощен:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

Внедрение нового шаблона версии 2.0 настоятельно рекомендуется; оно является обязательным для использования [миграций Entity Framework (EF) Core](xref:data/ef-mvc/migrations) и ряда других функций продукта. Например, при выполнении команды `Update-Database` из консоли диспетчера пакетов или команды `dotnet ef database update` в командной строке (для проектов, преобразованных в ASP.NET Core 2.0) возникает следующая ошибка:

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a>Все поставщики конфигурации

В проектах 1.x поставщики конфигурации добавлялись в приложение с помощью конструктора `Startup`. Для этого нужно было создать экземпляр `ConfigurationBuilder`, загрузить применимые поставщики (переменные сред, параметры приложений и т. д.) и инициализировать член `IConfigurationRoot`.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

В примере выше происходит загрузка члена `Configuration` с параметрами конфигурации из *appsettings.json* и любого файла *appsettings.\<Имя_среды\>.json*, соответствующего свойству `IHostingEnvironment.EnvironmentName`. Эти файлы располагаются по тому же пути, что и *Startup.cs*.

В проектах 2.0 стереотипный код конфигурации из проектов 1.x запускается "за кулисами". Например, переменные сред и параметры приложений загружаются при запуске. Эквивалентный код *Startup.cs* сокращается до инициализации `IConfiguration` с внедрением экземпляра.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

Чтобы удалить добавляемых `WebHostBuilder.CreateDefaultBuilder` поставщиков по умолчанию, вызовите метод `Clear` для свойства `IConfigurationBuilder.Sources` внутри `ConfigureAppConfiguration`. Чтобы снова добавить поставщиков, используйте метод `ConfigureAppConfiguration` в *Program.cs*.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

Конфигурацию, используемую в предыдущем фрагменте кода методом `CreateDefaultBuilder`, можно посмотреть [здесь](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).

Дополнительные сведения см. в разделе [Конфигурация в ASP.NET Core](xref:fundamentals/configuration/index).

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>Перенос кода инициализации базы данных

В проектах 1.x, где используется EF Core 1.x, такая команда, как `dotnet ef migrations add`, делает следующее.

1. Создает экземпляр `Startup`.
1. Вызывает метод `ConfigureServices`, чтобы зарегистрировать все службы с использованием вставки зависимостей (включая типы `DbContext`).
1. Выполняет свои требуемые задачи.

В проектах 2.0, где используется EF Core 2.0, вызывается `Program.BuildWebHost`, чтобы получить службы приложений. В отличие от 1.x, это также приводит к вызову `Startup.Configure`. Если ваше приложение 1.x вызвало код инициализации базы данных в своем методе `Configure`, могут возникнуть непредвиденные проблемы. Например, если база данных еще не существует, код заполнения запускается до выполнения команды миграции EF Core. Из-за этой проблемы, если база данных еще не существует, команда `dotnet ef migrations list` не срабатывает.

В качестве примера из версии 1.x возьмем следующий код инициализации заполнения в методе `Configure` из *Startup.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

В проектах 2.0 поместите вызов `SeedData.Initialize` в метод `Main` из *Program.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

Начиная с версии 2.0, делать в `BuildWebHost` что-либо помимо сборки и настройки веб-узла не рекомендуется. Все, что касается работы приложения, должно обрабатываться вне `BuildWebHost` &mdash; обычно в методе `Main` из *Program.cs*.

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a>Проверка параметра компиляции представлений Razor

Сокращение времени запуска приложений и уменьшение размеров публикуемых пакетов крайне важны. По этой причине в ASP.NET Core 2.0 по умолчанию включена [компиляция представлений Razor](xref:mvc/views/view-compilation).

Присваивать свойству `MvcRazorCompileOnPublish` значение true больше не нужно. Если вы не собираетесь отключать компиляцию представлений, это свойство можно удалить из файла *CSPROJ*.

Если проект предназначен для .NET Framework, необходимо по-прежнему явно указывать ссылку на пакет NuGet [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) в файле *CSPROJ*:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>Использование подсветки функций Application Insights

Простота настройки инструментария для обеспечения производительности приложений имеет большое значение. Теперь вам доступны новые функции подготовки [Application Insights](/azure/application-insights/app-insights-overview) в составе средств Visual Studio 2017.

При создании проектов ASP.NET Core 1.1 в Visual Studio 2017 служба Application Insights добавлялась по умолчанию. Если вы не используете пакет SDK для Application Insights напрямую вне файлов *Program.cs* и *Startup.cs*, выполните указанные ниже действия.

1. Для работы с .NET Core удалите из файла *CSPROJ* следующий узел `<PackageReference />`:

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. Для работы с .NET Core удалите вызов метода расширения `UseApplicationInsights` из файла *Program.cs*:

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. Удалите вызов API на стороне клиента Application Insights из файла *_Layout.cshtml*. Этот вызов включает две строки кода:

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

Если вы используете пакет SDK для Application Insights напрямую, продолжайте делать это. [Метапакет](xref:fundamentals/metapackage) версии 2.0 включает в себя последнюю версию Application Insights, поэтому при попытке сослаться на более старую версию возникает ошибка понижения уровня пакета.

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a>Внедрение улучшений проверки подлинности или Identity

В ASP.NET Core 2.0 реализована новая модель проверки подлинности и внесен ряд важных изменений в ASP.NET Core Identity. Если при создании проекта вы включили отдельные учетные записи пользователей либо вручную добавили проверку подлинности или Identity, см. сведения на странице [Миграция на другой метод проверки подлинности и Identity в ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Критические изменения в ASP.NET Core 2.0](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
