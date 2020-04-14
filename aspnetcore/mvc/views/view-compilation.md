---
title: Компиляция файлов Razor в ASP.NET Core
author: rick-anderson
description: Узнайте, как происходит компиляция файлов Razor в приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277277"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Компиляция файлов Razor в ASP.NET Core

Автор: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Файлы Razor с расширением *CSHTML* компилируются и во время сборки, и во время публикации с помощью [пакета SDK для Razor](xref:razor-pages/sdk). Компиляция runtime может быть опционально включена путем настройки проекта.

## <a name="razor-compilation"></a>Компиляция Razor

Сборка времени и время публикации компиляции файлов Razor включена по умолчанию SDK Razor. При включении компиляция времени выполнения дополняет компиляцию времени сборки, позволяя обновлять файлы Razor, если они редактируются.

## <a name="enable-runtime-compilation-at-project-creation"></a>Включить компиляцию времени выполнения при создании проекта

Шаблоны проектов Razor Pages и MVC включают опцию для включения компиляции времени выполнения при создании проекта. Эта опция поддерживается в ASP.NET Core 3.1 и позже.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В **создании нового ASP.NET журнале веб-приложений Core:**

1. Выберите шаблон проекта **Web Application** или Web **Application (Model-View-Controller).**
1. Выберите **флажок компиляции времени выполнения Enable Razor.**

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Используйте `-rrc` `--razor-runtime-compilation` опцию или шаблон. Например, следующая команда создает новый проект Razor Pages с включенной компиляцией времени выполнения:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Включение компиляции времени выполнения в существующем проекте

Для обеспечения компиляции времени выполнения для всех сред в существующем проекте:

1. Установите пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.
1. обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>. Пример:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Условно включить компиляцию времени выполнения в существующем проекте

Компиляцию в среде выполнения можно включить так, чтобы она была доступна только для локальной рабочей среды. Подобное условное включение гарантирует, что опубликованные выходные данные:

* используют скомпилированные представления;
* не включают наблюдатели файлов в рабочей среде.

Для обеспечения компиляции времени выполнения только в среде разработки:

1. Установите пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.
1. Измените раздел `environmentVariables` профиля запуска в *launchSettings.json:*
    * Проверка `ASPNETCORE_ENVIRONMENT` установлена `"Development"`на .
    * Присвойте параметру `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` значение `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.

В следующем примере компиляция времени выполнения включена в среде разработки для профилей `IIS Express` и `RazorPagesApp` запусков:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

В классе проекта не требуется `Startup` никаких изменений кода. В runtime ASP.NET Core ищет [атрибут HostingStartup на уровне сборки](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) в `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`. Атрибут `HostingStartup` определяет код запуска приложения для выполнения. Этот код запуска позволяет компиляцию времени выполнения.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [RazorCompileOnBuild и RazorCompileOnPublish](xref:razor-pages/sdk#properties) свойства.
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Пример [компиляции времени выполнения на GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) можно просмотреть пример, который показывает, что компиляция времени выполнения работает в проектах.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Файлы Razor с расширением *CSHTML* компилируются и во время сборки, и во время публикации с помощью [пакета SDK для Razor](xref:razor-pages/sdk). Компиляцию в среде выполнения при необходимости можно включить, настроив приложение.

## <a name="razor-compilation"></a>Компиляция Razor

Сборка времени и время публикации компиляции файлов Razor включена по умолчанию SDK Razor. При включении компиляция времени выполнения дополняет компиляцию времени сборки, позволяя обновлять файлы Razor, если они редактируются.

## <a name="runtime-compilation"></a>компиляция среды выполнения.

Чтобы включить компиляцию в среде выполнения для всех сред и режимов конфигурации, сделайте следующее:

1. Установите пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.

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

1. Условно сошлитесь на пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) с учетом активного значения `Configuration`:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов `AddRazorRuntimeCompilation`. Условно выполните `AddRazorRuntimeCompilation`, чтобы он выполнялся только в режиме отладки, если для переменной `ASPNETCORE_ENVIRONMENT` задано значение `Development`:

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [RazorCompileOnBuild и RazorCompileOnPublish](xref:razor-pages/sdk#properties) свойства.
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Пример [компиляции времени выполнения на GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) можно просмотреть пример, который показывает, что компиляция времени выполнения работает в проектах.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Файл Razor компилируется в среде выполнения при вызове связанной страницы Razor или представления MVC. Файлы Razor компилируются и во время сборки, и во время публикации с помощью [пакета SDK для Razor](xref:razor-pages/sdk).

## <a name="razor-compilation"></a>Компиляция Razor

Компиляция файлов Razor во время сборки и публикации включена по умолчанию с помощью пакета SDK для Razor. Редактирование файлов Razor после их обновления поддерживается во время сборки. По умолчанию только скомпилированные файлы *Views.dll*, а не *.cshtml*, и сборки, необходимые для компиляции файлов Razor, развертываются с приложением.

> [!IMPORTANT]
> Средство предварительной компиляции является нерекомендуемым и будет удалено в ASP.NET Core 3.0. Мы советуем перейти на [пакет SDK для Razor](xref:razor-pages/sdk).
>
> Пакет SDK для Razor применяется только в том случае, если в файле проекта не заданы свойства предварительной компиляции. Например, если в *CSPROJ*-файле для свойства `MvcRazorCompileOnPublish` установлено значение `true`, пакет SDK для Razor будет отключен.

## <a name="runtime-compilation"></a>компиляция среды выполнения.

Компиляция во время сборки дополняется компиляцией файлов Razor в среде выполнения. ASP.NET Core MVC будет перекомпилировать файлы Razor, когда содержимое файла *.cshtml* меняется.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
