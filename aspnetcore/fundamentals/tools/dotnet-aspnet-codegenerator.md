---
title: Команда dotnet aspnet-codegenerator
author: rick-anderson
description: Команда dotnet aspnet-codegenerator формирует шаблоны для проектов ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "94920707"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

Команда `dotnet aspnet-codegenerator` запускает подсистему формирования шаблонов ASP.NET Core. Команда `dotnet aspnet-codegenerator` нужна только для командной строки. Она не требуется для формирования шаблонов в Visual Studio.

## <a name="install-and-update-aspnet-codegenerator"></a>Установка и обновление aspnet-codegenerator

Установите [пакет SDK для .NET](https://dotnet.microsoft.com/download).

`dotnet-aspnet-codegenerator` — [глобальное средство](/dotnet/core/tools/global-tools), которое нужно установить. Следующая команда позволяет установить последнюю стабильную версию средства `dotnet-aspnet-codegenerator`:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Приведенная ниже команда позволяет обновить `dotnet-aspnet-codegenerator` до последней стабильной версии, доступной из установленных пакетов SDK для .NET Core.

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a>Удаление aspnet-codegenerator

Для устранения проблем, возможно, потребуется удалить `aspnet-codegenerator`. Например, если вы установили предварительную версию `aspnet-codegenerator`, удалите ее перед установкой выпущенной версии.

Следующая команда позволяет удалить средство `dotnet-aspnet-codegenerator` и установить последнюю стабильную версию:

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Краткий обзор

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Описание

Глобальная команда `dotnet aspnet-codegenerator` запускает генератор кода и подсистему формирования шаблонов ASP.NET Core.

## <a name="arguments"></a>Аргументы

`generator`

Запускаемый генератор кода. Доступны такие генераторы:

| Generator  | Операция                                                            |
| ---------- | -------------------------------------------------------------------- |
| площадь       | [Формирует шаблон области](xref:mvc/controllers/areas)                      |
| контроллер | [Формирует шаблон контроллера](xref:tutorials/first-mvc-app/adding-model)  |
| удостоверение   | [Формирует шаблон Identity](xref:security/authentication/scaffold-identity) |
| razorpage  | [Формирует шаблон страниц Razor Pages](xref:tutorials/razor-pages/model)            |
| view       | [Формирует шаблон представления](xref:mvc/views/overview)                          |

## <a name="options"></a>Параметры

`-n|--nuget-package-dir`

Позволяет указать каталог пакета NuGet.

`-c|--configuration {Debug|Release}`

Определяет конфигурацию сборки. Значение по умолчанию — `Debug`.

`-tfm|--target-framework`

[Целевая платформа](/dotnet/standard/frameworks) для использования. Например, `net46`.

`-b|--build-base-path`

Базовый путь сборки.

`-h|--help`

Выводит краткую справку по команде.

`--no-build`

Не выполняет сборку проекта перед запуском. Он также неявно задает флаг `--no-restore`.

`-p|--project <PATH>`

Задает путь к запускаемому файлу проекта (имя папки или полный путь). Если значение не задано, по умолчанию используется текущий каталог.

## <a name="generator-options"></a>Параметры генератора

В следующих разделах подробно описаны параметры, доступные для поддерживаемых генераторов.

* Область
* Контроллер
* Identity  
* Razorpage
* Просмотр

<a name="area"></a>

### <a name="area-options"></a>Параметры области

Это средство предназначено для веб-проектов ASP.NET Core с контроллерами и представлениями. Оно не предназначено для приложений Razor Pages.

Использование: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Приведенная выше команда создает такие папки:

* *Области*
  * *AreaNameToGenerate*
    * *Контроллеры*
    * *Данные*
    * *Models*
    * *Представления*

<a name="ctl"></a>

### <a name="controller-options"></a>Параметры контроллера

В таблице ниже перечислены параметры для `aspnet-codegenerator` `controller` и `razorpage`.

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

В таблице ниже перечислены параметры только для `aspnet-codegenerator controller`.

| Параметр                         | Описание                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| --controllerName или -name      | Имя контроллера.                                                                                   |
| --useAsyncActions или -async    | Создание асинхронных действий контроллера.                                                                        |
| --noViews или -nv               | Представления **не** создаются.                                                                                    |
| --restWithNoViews или -api      | Создание контроллера с API в стиле REST. Используется `noViews`, а все параметры, связанные с представлением, игнорируются. |
| --readWriteActions или -actions | Создание контроллера с действиями чтения и записи без модели.                                              |

Чтобы получить справку по команде `aspnet-codegenerator controller`, используйте параметр `-h`.

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Пример `dotnet aspnet-codegenerator controller` см. в разделе [Создание модели фильма](xref:tutorials/first-mvc-app/adding-model).

### <a name="no-locrazorpage"></a>Razorpage

<a name="rp"></a>

Шаблоны для страниц Razor Pages можно формировать по отдельности. Для этого нужно указать имя новой страницы и используемый шаблон. Поддерживаются такие шаблоны:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Например, в следующей команде используется шаблон Edit для создания *MyEdit.cshtml* и *MyEdit.cshtml.cs*:

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Создаются следующие шаблоны, а шаблон и имя созданного файла обычно не указываются:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

В таблице ниже перечислены параметры для `aspnet-codegenerator` `razorpage` и `controller`.

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

В таблице ниже перечислены параметры только для `aspnet-codegenerator razorpage`.

| Параметр                        | Описание                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| --namespaceName или -namespace | Имя пространства имен, используемого для созданной модели PageModel.                          |
| --partialView или -partial     | Создание частичного представления. Если указан этот параметр, параметры макета -l и -udl игнорируются. |
| --noPageModel или -npm         | Параметр, при использовании которого не создается класс PageModel для пустого шаблона.                           |

Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`.

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Пример `dotnet aspnet-codegenerator razorpage` см. в разделе [Создание модели фильма](xref:tutorials/razor-pages/model).

### Identity

См. [Формирование шаблонов Identity](xref:security/authentication/scaffold-identity)
