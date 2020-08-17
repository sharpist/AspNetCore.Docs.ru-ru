---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Сведения о применении в ASP.NET Core функции Razor Pages, которая делает создание кодов сценариев для страниц проще и эффективнее по сравнению с MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
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
uid: razor-pages/sdk
ms.openlocfilehash: b960460a50558a11bc47f9a1844931aa32e3d696
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021423"
---
# <a name="aspnet-core-no-locrazor-sdk"></a>ASP.NET Core Razor SDK

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

## <a name="overview"></a>Обзор

[!INCLUDE[](~/includes/2.1-SDK.md)] включает пакет SDK MSBuild `Microsoft.NET.Sdk.Razor` (пакет SDK для Razor). Пакет SDK для Razor:

::: moniker range=">= aspnetcore-3.0"

* Требуется для сборки, упаковки и публикации проектов, содержащих файлы [Razor](xref:mvc/views/razor) для проектов ASP.NET Core на основе MVC или проектов [Blazor](xref:blazor/index).
* Включает набор предопределенных целевых объектов, свойств и элементов, которые позволяют настраивать параметры компиляции файлов Razor (*CSHTML* или *RAZOR*).

Пакет SDK для Razor включает элементы `Content` с атрибутами `Include`, для которых установлено значение `**\*.cshtml` и шаблоны подстановки `**\*.razor`. Публикуются соответствующие файлы.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Стандартизирует процесс создания, упаковки и публикации проектов, содержащих файлы [Razor](xref:mvc/views/razor), для проектов на основе MVC ASP.NET.
* Включает набор предопределенных целевых объектов, свойств и элементов, которые позволяют настраивать параметры компиляции файлов Razor.

Пакет SDK для Razor включает элементы `Content` с атрибутами `Include`, для которых установлены шаблоны подстановки `**\*.cshtml`. Публикуются соответствующие файлы.

::: moniker-end

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-no-locrazor-sdk"></a>Использование пакета SDK для Razor

Большинству веб-приложений не требуется явная ссылка на пакет SDK для Razor.

::: moniker range=">= aspnetcore-3.0"

Чтобы использовать пакет SDK для Razor для построения библиотек классов, содержащих представления Razor или Razor Pages, мы рекомендуем начать с шаблона проекта "Библиотека классов Razor" (RCL). Для RCL, который используется для сборки файлов Blazor (*RAZOR*), требуется ссылка на пакет [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components). RCL, который используется для сборки представлений или страниц Razor (файлы *CSHTML*), требует как минимум `netcoreapp3.0` или более поздней версии и имеет ссылку `FrameworkReference` на метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) в файле проекта.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Чтобы использовать пакет SDK для Razor для построения библиотек классов, содержащих представления Razor или страницы Razor:

* Используйте `Microsoft.NET.Sdk.Razor` вместо `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Обычно требуется ссылка на пакет `Microsoft.AspNetCore.Mvc` для получения дополнительных зависимостей, необходимых для сборки и компиляции страниц Razor Pages и представлений Razor. Как минимум, в проекте нужны ссылки на следующие пакеты:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Пакет `Microsoft.AspNetCore.Razor.Design` предоставляет задачи компиляции Razor и целевые объекты для проекта.

  Предыдущие пакеты включены в `Microsoft.AspNetCore.Mvc`. В приведенном ниже коде демонстрируется файл проекта, который использует пакет SDK для Razor, чтобы создавать файлы Razor для приложения Razor Pages ASP.NET Core:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Пакеты `Microsoft.AspNetCore.Razor.Design` и `Microsoft.AspNetCore.Mvc.Razor.Extensions` включены в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Однако ссылка на пакет без `Microsoft.AspNetCore.App` версии предоставляет приложению метапакет, которое не включает последнюю версию `Microsoft.AspNetCore.Razor.Design`. Проекты должны ссылаться на последовательную версию `Microsoft.AspNetCore.Razor.Design` (или `Microsoft.AspNetCore.Mvc`), чтобы были включены последние исправления времени сборки для Razor. Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Свойства

Следующие свойства управляют поведением пакета SDK для Razor в ходе создания проекта:

* `RazorCompileOnBuild`. Если имеет значение `true`, компилирует и создает сборку Razor при создании проекта. По умолчанию — `true`.
* `RazorCompileOnPublish`. Если имеет значение `true`, компилирует и создает сборку Razor при публикации проекта. По умолчанию — `true`.

Свойства и элементы в следующей таблице используются для настройки входных и выходных данных в пакете SDK для Razor.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Начиная с ASP.NET Core 3.0 представления MVC или Razor Pages не обслуживаются по умолчанию, если в файле проекта отключены свойства `RazorCompileOnBuild` или `RazorCompileOnPublish` MSBuild. Приложения должны добавить явную ссылку на пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation), если приложение использует компиляцию среды выполнения для обработки файлов *CSHTML*.

::: moniker-end

| Элементы | Описание |
| ----- | ----------- |
| `RazorGenerate` | Элементы (файлы *CSHTML*), которые являются входными данными для создания кода. |
| `RazorComponent` | Элементы (файлы *RAZOR*), которые являются входными данными для создания кода компонентов Razor. |
| `RazorCompile` | Элементы (файлы *CS*), которые являются входными данными для целей компиляции Razor. Используйте этот элемент `ItemGroup`, чтобы указать дополнительные файлы для компиляции в сборку Razor. |
| `RazorTargetAssemblyAttribute` | Элементы, используемые для создания атрибутов для сборки Razor. Пример:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Элементы, добавленные в качестве внедренных ресурсов к созданной сборке Razor. |

::: moniker range=">= aspnetcore-3.0"

| Свойство. | Описание |
| -------- | ----------- |
| `RazorTargetName` | Имя файла (без расширения) для сборки, созданной Razor. |
| `RazorOutputPath` | Выходной каталог Razor. |
| `RazorCompileToolset` | Используется для определения набора инструментов для построения сборки Razor. Допустимые значения: `Implicit`, `RazorSDK` и `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Значение по умолчанию — `true`. Если указано значение `true`, включает файлы *WEB.CONFIG*, *JSON* и *CSHTML* в качестве содержимого проекта. При ссылке через `Microsoft.NET.Sdk.Web` также включает все файлы в *wwwroot* и файлы конфигурации. |
| `EnableDefaultRazorGenerateItems` | При значении `true` включает файлы *.cshtml* из элементов `Content` в элементы `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | При значении `true` создает файл *CS* с атрибутами, заданными `RazorAssemblyAttribute`, и включает его в выходные данные компиляции. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | При значении `true` добавляет стандартный набор атрибутов сборки в `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | При значении `true` копирует элементы `RazorGenerate` (файлы *CSHTML*) в каталог публикации. Обычно опубликованному приложению не нужны файлы Razor, если они участвуют в компиляции во время сборки или публикации. По умолчанию — `false`. |
| `PreserveCompilationReferences` | При значении `true` копирует элементы базовой сборки в каталог публикации. Обычно опубликованному приложению не нужны базовые сборки, если компиляция Razor происходит во время сборки или публикации. Задайте значение `true`, если для опубликованного приложения требуется компиляция в среде выполнения. Например, задайте значение `true`, если приложение изменяет файлы *CSHTML* во время выполнения или использует внедренные представления. По умолчанию — `false`. |
| `IncludeRazorContentInPack` | При значении `true` все элементы содержимого Razor (файлы *CSHTML*) помечаются для включения в создаваемый пакет NuGet. По умолчанию — `false`. |
| `EmbedRazorGenerateSources` | При значении `true` элементы RazorGenerate ( *.cshtml*) добавляются в виде внедренных файлов к создаваемой сборке Razor. По умолчанию — `false`. |
| `UseRazorBuildServer` | При значении `true` использует серверный процесс постоянной сборки для разгрузки работы по созданию кода. По умолчанию используется значение `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | При значении `true` пакет SDK создает дополнительные атрибуты, используемые MVC во время выполнения для обнаружения частей приложения. |
| `DefaultWebContentItemExcludes` | Шаблон подстановки для элементов, которые должны быть исключены из группы элементов `Content` в проектах, предназначенных для веб-пакета или пакета SDK для Razor |
| `ExcludeConfigFilesFromBuildOutput` | При значении `true` файлы *CONGIF* и *JSON* не копируются в выходной каталог сборки. |
| `AddRazorSupportForMvc` | При значении `true` настраивается пакет SDK для Razor для добавления поддержки конфигурации MVC, которая необходима при создании приложений, содержащих представления MVC или Razor Pages. Это свойство неявно задано для проектов .NET Core 3.0 или более поздней версии, предназначенных для веб-пакета SDK |
| `RazorLangVersion` | Используемая версия языка Razor. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Свойство. | Описание |
| -------- | ----------- |
| `RazorTargetName` | Имя файла (без расширения) для сборки, созданной Razor. |
| `RazorOutputPath` | Выходной каталог Razor. |
| `RazorCompileToolset` | Используется для определения набора инструментов для построения сборки Razor. Допустимые значения: `Implicit`, `RazorSDK` и `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Значение по умолчанию — `true`. Если указано значение `true`, включает файлы *WEB.CONFIG*, *JSON* и *CSHTML* в качестве содержимого проекта. При ссылке через `Microsoft.NET.Sdk.Web` также включает все файлы в *wwwroot* и файлы конфигурации. |
| `EnableDefaultRazorGenerateItems` | При значении `true` включает файлы *.cshtml* из элементов `Content` в элементы `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | При значении `true` создает файл *CS* с атрибутами, заданными `RazorAssemblyAttribute`, и включает его в выходные данные компиляции. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | При значении `true` добавляет стандартный набор атрибутов сборки в `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | При значении `true` копирует элементы `RazorGenerate` (файлы *CSHTML*) в каталог публикации. Обычно опубликованному приложению не нужны файлы Razor, если они участвуют в компиляции во время сборки или публикации. По умолчанию — `false`. |
| `CopyRefAssembliesToPublishDirectory` | При значении `true` копирует элементы базовой сборки в каталог публикации. Обычно опубликованному приложению не нужны базовые сборки, если компиляция Razor происходит во время сборки или публикации. Задайте значение `true`, если для опубликованного приложения требуется компиляция в среде выполнения. Например, задайте значение `true`, если приложение изменяет файлы *CSHTML* во время выполнения или использует внедренные представления. По умолчанию — `false`. |
| `IncludeRazorContentInPack` | При значении `true` все элементы содержимого Razor (файлы *CSHTML*) помечаются для включения в создаваемый пакет NuGet. По умолчанию — `false`. |
| `EmbedRazorGenerateSources` | При значении `true` элементы RazorGenerate ( *.cshtml*) добавляются в виде внедренных файлов к создаваемой сборке Razor. По умолчанию — `false`. |
| `UseRazorBuildServer` | При значении `true` использует серверный процесс постоянной сборки для разгрузки работы по созданию кода. По умолчанию используется значение `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | При значении `true` пакет SDK создает дополнительные атрибуты, используемые MVC во время выполнения для обнаружения частей приложения. |
| `DefaultWebContentItemExcludes` | Шаблон подстановки для элементов, которые должны быть исключены из группы элементов `Content` в проектах, предназначенных для веб-пакета или пакета SDK для Razor |
| `ExcludeConfigFilesFromBuildOutput` | При значении `true` файлы *CONGIF* и *JSON* не копируются в выходной каталог сборки. |
| `AddRazorSupportForMvc` | При значении `true` настраивается пакет SDK для Razor для добавления поддержки конфигурации MVC, которая необходима при создании приложений, содержащих представления MVC или Razor Pages. Это свойство неявно задано для проектов .NET Core 3.0 или более поздней версии, предназначенных для веб-пакета SDK |
| `RazorLangVersion` | Используемая версия языка Razor. |

::: moniker-end

Дополнительные сведения о свойствах см. в статье [MSBuild Properties](/visualstudio/msbuild/msbuild-properties) (Свойства MSBuild).

### <a name="targets"></a>Целевые объекты

Пакет SDK для Razor определяет два основных целевых объекта.

* `RazorGenerate`. Код создает файлы *CS* из элементов `RazorGenerate`. Используйте свойство `RazorGenerateDependsOn`, чтобы указать дополнительные целевые объекты, которые могут выполняться до или после этого целевого объекта.
* `RazorCompile`. Компилирует созданные файлы *CS* в сборку Razor. Используйте `RazorCompileDependsOn`, чтобы указать дополнительные целевые объекты, которые могут выполняться до или после этого целевого объекта.
* `RazorComponentGenerate`. Код создает файлы *CS* для элементов `RazorComponent`. Используйте свойство `RazorComponentGenerateDependsOn`, чтобы указать дополнительные целевые объекты, которые могут выполняться до или после этого целевого объекта.

### <a name="runtime-compilation-of-no-locrazor-views"></a>Компиляция среды выполнения представлений Razor

* По умолчанию пакет SDK для Razor не публикует базовые сборки, необходимые для компиляции среды выполнения. Это приведет к сбою компиляции, если модель приложения зависит от компиляции среды выполнения &mdash; например, приложение использует внедренные представления или меняет представления после публикации. Установите для `CopyRefAssembliesToPublishDirectory` значение `true`, чтобы продолжить публикацию базовых сборок.

* Для веб-приложений убедитесь, что приложение предназначено для пакета SDK `Microsoft.NET.Sdk.Web`.

## <a name="no-locrazor-language-version"></a>Версия языка Razor

При использовании пакета SDK `Microsoft.NET.Sdk.Web` версия языка Razor выводится из целевой версии платформы приложения. Для проектов, предназначенных для SDK `Microsoft.NET.Sdk.Razor`, или в редких случаях, когда приложению требуется другая версия языка Razor, отличная от выводимого значения, можно настроить версию, задав свойство `<RazorLangVersion>` в файле проекта приложения.

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Версия языка Razor тесно интегрирована с версией среды выполнения, для которой она была создана. Выбор языковой версии, не предназначенной для среды выполнения, не поддерживается и, скорее всего, приведет к ошибкам сборки.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Дополнения к формату CSPROJ для .NET Core](/dotnet/core/tools/csproj)
* [Общие элементы проектов MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
