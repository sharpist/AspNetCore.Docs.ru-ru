---
title: Razor Компиляция файла в ASP.NET Core
author: rick-anderson
description: Узнайте Razor , как происходит компиляция файлов в приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- appsettings.json
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
uid: mvc/views/view-compilation
ms.openlocfilehash: 77ca96b329136ee044ab6fc5f6b5ebb5b67fe64c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059082"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a>Razor Компиляция файла в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-3.1"

Razorфайлы с расширением *. cshtml* компилируются как во время сборки, так и при публикации с помощью [ Razor пакета SDK](xref:razor-pages/sdk). Компиляцию среды выполнения можно дополнительно включить, настроив проект.

## <a name="no-locrazor-compilation"></a>Razor компиляции

Компиляция файлов во время сборки и времени публикации Razor по умолчанию включена Razor пакетом SDK. При включении компиляция среды выполнения дополняет компиляцию во время сборки, позволяя Razor обновлять файлы, если они редактируются.

## <a name="enable-runtime-compilation-at-project-creation"></a>Включить компиляцию во время выполнения при создании проекта

RazorШаблоны проектов Pages и MVC включают в себя возможность включения компиляции во время создания проекта. Этот параметр поддерживается в ASP.NET Core 3,1 и более поздних версиях.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В диалоговом окне **Создание нового ASP.NET Core веб-приложения** выполните следующие действия.

1. Выберите шаблон проекта веб- **приложение** или **веб-приложение (модель-представление-контроллер)** .
1. Установите флажок **включить Razor компиляцию во время выполнения** .

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Используйте `-rrc` `--razor-runtime-compilation` параметр шаблона или. Например, следующая команда создает новый Razor проект страниц с включенной компиляцией среды выполнения:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Включение компиляции среды выполнения в существующем проекте

Включение компиляции во время выполнения для всех сред в существующем проекте:

1. Установите [Microsoft. AspNetCore. MVC. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) Пакет NuGet рунтимекомпилатион.
1. обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>. Пример:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Условно включить компиляцию среды выполнения в существующем проекте

Компиляцию в среде выполнения можно включить так, чтобы она была доступна только для локальной рабочей среды. Подобное условное включение гарантирует, что опубликованные выходные данные:

* используют скомпилированные представления;
* не включают наблюдатели файлов в рабочей среде.

Включение компиляции во время выполнения только в среде разработки:

1. Установите [Microsoft. AspNetCore. MVC. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) Пакет NuGet рунтимекомпилатион.
1. Измените раздел "профиль запуска" `environmentVariables` в *launchSettings.js* :
    * `ASPNETCORE_ENVIRONMENT`Для параметра проверить задано значение `"Development"` .
    * Присвойте свойству `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` значение `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.

В следующем примере компиляция среды выполнения включается в среде разработки для `IIS Express` `RazorPagesApp` профилей запуска и.

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

В классе проекта не требуется вносить изменения в код `Startup` . Во время выполнения ASP.NET Core выполняет поиск [атрибута хостингстартуп на уровне сборки](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) в `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` . `HostingStartup`Атрибут указывает код запуска приложения для выполнения. Код запуска включает компиляцию среды выполнения.

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a>Включение компиляции среды выполнения для Razor библиотеки классов

Рассмотрим ситуацию, в которой Razor проект страниц ссылается на [ Razor БИБЛИОТЕКУ классов (РКЛ)](xref:razor-pages/ui-class) с именем *микласслиб* . РКЛ содержит файл *_layout. cshtml* , который используется всеми проектами MVC и Razor pages вашей команды. Необходимо включить компиляцию среды выполнения для файла *_layout. cshtml* в этой РКЛ. Внесите следующие изменения в Razor проект страниц.

1. Включите компиляцию среды выполнения с инструкциями по [условию включить компиляцию среды выполнения в существующем проекте](#conditionally-enable-runtime-compilation-in-an-existing-project).
1. Настройте параметры компиляции среды выполнения в `Startup.ConfigureServices` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    В приведенном выше коде создается абсолютный путь к *микласслиб* РКЛ. [API фисикалфилепровидер](xref:fundamentals/file-providers#physicalfileprovider) используется для поиска каталогов и файлов по этому абсолютному пути. Наконец, `PhysicalFileProvider` экземпляр добавляется в коллекцию поставщиков файлов, которая разрешает доступ к ФАЙЛАМ РКЛ *. cshtml* .

## <a name="additional-resources"></a>Дополнительные ресурсы

* Свойства [ Razor компилеонбуилд и Razor компилеонпублиш](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razorфайлы с расширением *. cshtml* компилируются как во время сборки, так и при публикации с помощью [ Razor пакета SDK](xref:razor-pages/sdk). Компиляцию в среде выполнения при необходимости можно включить, настроив приложение.

## <a name="no-locrazor-compilation"></a>Razor компиляции

Компиляция файлов во время сборки и времени публикации Razor по умолчанию включена Razor пакетом SDK. При включении компиляция среды выполнения дополняет компиляцию во время сборки, позволяя Razor обновлять файлы, если они редактируются.

## <a name="runtime-compilation"></a>компиляция среды выполнения.

Чтобы включить компиляцию в среде выполнения для всех сред и режимов конфигурации, сделайте следующее:

1. Установите [Microsoft. AspNetCore. MVC. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) Пакет NuGet рунтимекомпилатион.

1. обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>. Пример:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Условное включение компиляции в среде выполнения

Компиляцию в среде выполнения можно включить так, чтобы она была доступна только для локальной рабочей среды. Подобное условное включение гарантирует, что опубликованные выходные данные:

* используют скомпилированные представления;
* имеют меньший размер;
* не включают наблюдатели файлов в рабочей среде.

Чтобы включить компиляцию в среде выполнения для конкретной среды и конкретного режима конфигурации, сделайте следующее:

1. Условная ссылка на [Microsoft. AspNetCore. MVC. Razor . Рунтимекомпилатион](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) пакет на основе активного `Configuration` значения:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов `AddRazorRuntimeCompilation`. Условно выполните `AddRazorRuntimeCompilation`, чтобы он выполнялся только в режиме отладки, если для переменной `ASPNETCORE_ENVIRONMENT` задано значение `Development`:

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* Свойства [ Razor компилеонбуилд и Razor компилеонпублиш](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Пример, демонстрирующий работу компиляции во время выполнения проектов, см. в [примере компиляции среды выполнения на GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

RazorФайл компилируется во время выполнения, когда Razor вызывается связанная страница или представление MVC. Razorфайлы компилируются как во время сборки, так и при публикации с помощью [ Razor пакета SDK](xref:razor-pages/sdk).

## <a name="no-locrazor-compilation"></a>Razor компиляции

Компиляция файлов и времени публикации Razor по умолчанию включается Razor пакетом SDK. Редактирование Razor файлов после их обновления поддерживается во время сборки. По умолчанию в приложении развертываются только скомпилированные *Views.dll* и файлы с *. cshtml* или ссылки на сборки, необходимые для компиляции Razor файлов.

> [!IMPORTANT]
> Средство предварительной компиляции является нерекомендуемым и будет удалено в ASP.NET Core 3.0. Мы рекомендуем переходить на [ Razor пакет SDK](xref:razor-pages/sdk).
>
> RazorПакет SDK эффективен, только если в файле проекта не заданы свойства, относящиеся к предварительной компиляции. Например, *.csproj* `MvcRazorCompileOnPublish` чтобы `true` Отключить Razor пакет SDK, присвоив свойству файла CSPROJ значение.

## <a name="runtime-compilation"></a>компиляция среды выполнения.

Компиляция во время сборки дополняется компиляцией файлов в среде выполнения Razor . ASP.NET Core MVC будет перекомпилировать Razor файлы при изменении содержимого файла *CSHTML* .

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
