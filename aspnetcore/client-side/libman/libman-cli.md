---
title: Использование интерфейса командной строки LibMan с ASP.NET Core
author: scottaddie
description: Сведения об использовании интерфейса командной строки LibMan в проекте ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
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
uid: client-side/libman/libman-cli
ms.openlocfilehash: dad9136439b61ad98523061d181fe44d3bf1273d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054753"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Использование интерфейса командной строки LibMan с ASP.NET Core

Автор: [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)

Интерфейс командной строки [LibMan](xref:client-side/libman/index) (LibMan CLI) — это кроссплатформенная программа, которая поддерживается везде, где поддерживается .NET Core.

## <a name="prerequisites"></a>Предварительные требования

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Установка

Чтобы установить LibMan CLI, выполните следующую команду:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

[Глобальное средство .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) устанавливается из пакета NuGet [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/).

Чтобы установить LibMan CLI из определенного источника пакета NuGet, выполните следующую команду:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

В предыдущем примере глобальное средство .NET Core устанавливается из файла *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* на локальном компьютере Windows.

## <a name="usage"></a>Использование

После успешной установки CLI можно использовать следующую команду:

```console
libman
```

Чтобы узнать установленную версию CLI, выполните следующую команду:

```console
libman --version
```

Чтобы просмотреть доступные команды CLI, выполните следующую команду:

```console
libman --help
```

Приведенная выше команда выводит результат наподобие следующего:

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

Доступные команды CLI описываются в следующих разделах.

## <a name="initialize-libman-in-the-project"></a>Инициализация LibMan в проекте

Команда `libman init` создает файл *libman.json*, если он еще не существует. Файл создается с содержимым шаблона по умолчанию.

### <a name="synopsis"></a>Краткий обзор

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Параметры

Для команды `libman init` доступны следующие параметры:

* `-d|--default-destination <PATH>`

  Путь относительно текущей папки. Файлы библиотеки устанавливаются в этом расположении, если в файле *libman.json* свойство `destination` для библиотеки не задано. Значение `<PATH>` записывается в свойство `defaultDestination` в файле *libman.json*.

* `-p|--default-provider <PROVIDER>`

  Поставщик, который будет использоваться, если поставщик для данной библиотеки не указан. Значение `<PROVIDER>` записывается в свойство `defaultProvider` в файле *libman.json*. Замените `<PROVIDER>` одним из следующих значений:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Примеры

Чтобы создать файл *libman.json* в проекте ASP.NET Core, выполните указанные ниже действия.

* Перейдите в корневой каталог проекта.
* Выполните следующую команду:

  ```console
  libman init
  ```

* Введите имя поставщика по умолчанию или нажмите клавишу `Enter`, чтобы использовать поставщик CDNJS по умолчанию. Допустимы следующие значения:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Команда libman init — поставщик по умолчанию](_static/libman-init-provider.png)

В корневой каталог проекта добавляется файл *libman.json* со следующим содержимым:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Добавление файлов библиотеки

Команда `libman install` скачивает и устанавливает файлы библиотеки в проект. Если файл *libman.json* еще не существует, он создается. В файле *libman.json* сохраняются данные конфигурации для файлов библиотеки.

### <a name="synopsis"></a>Краткий обзор

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Аргументы

`LIBRARY`

Имя устанавливаемой библиотеки. В имени может использоваться нотация номера версии (например, `@1.2.0`).

### <a name="options"></a>Параметры

Для команды `libman install` доступны следующие параметры:

* `-d|--destination <PATH>`

  Расположение для установки библиотеки. Если не указано, используется расположение по умолчанию. Если в файле *libman.json* свойство `defaultDestination` не указано, этот параметр является обязательным.

* `--files <FILE>`

  Укажите имя файла, который необходимо установить из библиотеки. Если не указано, устанавливаются все файлы из библиотеки. Для каждого устанавливаемого файла необходимо задать один параметр `--files`. Также поддерживаются относительные пути. Например, `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Имя поставщика, используемого для получения библиотеки. Замените `<PROVIDER>` одним из следующих значений:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Если значение не указано, используется свойство `defaultProvider` из файла *libman.json*. Если в файле *libman.json* свойство `defaultProvider` не указано, этот параметр является обязательным.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Примеры

Рассмотрим следующий файл *libman.json*:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Чтобы установить файл *jquery.min.js* jQuery версии 3.2.1 в папку *wwwroot/scripts/jquery* с использованием поставщика CDNJS, выполните следующую команду:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

Содержимое файла *libman.json* выглядит примерно так:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

Чтобы установить файлы *calendar.js* и *calendar.css* из папки *C:\\temp\\contosoCalendar\\* с использованием поставщика файловой системы, выполните следующую команду:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

Приведенный ниже запрос появляется по двум причинам:

* В файле *libman.json* нет свойства `defaultDestination`.
* Команда `libman install` не содержит параметра `-d|--destination`.

![Команда libman install — назначение](_static/libman-install-destination.png)

После принятия назначения по умолчанию содержимое файла *libman.json* будет выглядеть примерно так:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>Восстановление файлов библиотек

Команда `libman restore` устанавливает файлы библиотеки, определенные в файле *libman.json*. Действуют следующие правила.

* Если файла *libman.json* нет в корневом каталоге проекта, возвращается ошибка.
* Если для библиотеки указан поставщик, свойство `defaultProvider` в файле *libman.json* игнорируется.
* Если для библиотеки указано назначение, свойство `defaultDestination` в файле *libman.json* игнорируется.

### <a name="synopsis"></a>Краткий обзор

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Параметры

Для команды `libman restore` доступны следующие параметры:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Примеры

Чтобы восстановить файлы библиотеки, определенные в файле *libman.json*, выполните следующую команду:

```console
libman restore
```

## <a name="delete-library-files"></a>Удаление файлов библиотек

Команда `libman clean` удаляет файлы библиотек, восстановленные ранее с помощью LibMan. Папки, которые становятся пустыми после выполнения этой операции, удаляются. Конфигурации, связанные с файлами библиотек, в свойстве `libraries` файла *libman.json* не удаляются.

### <a name="synopsis"></a>Краткий обзор

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Параметры

Для команды `libman clean` доступны следующие параметры:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Примеры

Чтобы удалить файлы библиотек, установленные с помощью LibMan, выполните следующую команду:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Удаление файлов библиотек

Команда `libman uninstall` делает следующее:

* удаляет все файлы, связанные с указанной библиотекой, из назначения в файле *libman.json*;
* удаляет связанную конфигурацию библиотеки из файла *libman.json*.

В следующих случаях возникает ошибка:

* файла *libman.json* нет в корневом каталоге проекта;
* указанная библиотека не существует.

Если установлено несколько библиотек с одним и тем же именем, вам будет предложено выбрать одну из них.

### <a name="synopsis"></a>Краткий обзор

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Аргументы

`LIBRARY`

Имя удаляемой библиотеки. В имени может использоваться нотация номера версии (например, `@1.2.0`).

### <a name="options"></a>Параметры

Для команды `libman uninstall` доступны следующие параметры:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Примеры

Рассмотрим следующий файл *libman.json*:

[!code-json[](samples/LibManSample/libman.json)]

* Для удаления jQuery можно выполнить любую из следующих команд:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Чтобы удалить файлы Lodash, установленные с помощью поставщика `filesystem`, выполните следующую команду:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Обновление версии библиотеки

Команда `libman update` обновляет библиотеку, установленную с помощью LibMan, до указанной версии.

В следующих случаях возникает ошибка:

* файла *libman.json* нет в корневом каталоге проекта;
* указанная библиотека не существует.

Если установлено несколько библиотек с одним и тем же именем, вам будет предложено выбрать одну из них.

### <a name="synopsis"></a>Краткий обзор

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Аргументы

`LIBRARY`

Имя обновляемой библиотеки.

### <a name="options"></a>Параметры

Для команды `libman update` доступны следующие параметры:

* `-pre`

  Получение последней предварительной версии библиотеки.

* `--to <VERSION>`

  Получение определенной версии библиотеки.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Примеры

* Чтобы обновить jQuery до последней версии, выполните следующую команду:

  ```console
  libman update jquery
  ```

* Чтобы обновить jQuery до версии 3.3.1, выполните следующую команду:

  ```console
  libman update jquery --to 3.3.1
  ```

* Чтобы обновить jQuery до последней предварительной версии, выполните следующую команду:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Управление кэшем библиотек

Команда `libman cache` управляет кэшем библиотек LibMan. Поставщик `filesystem` не использует кэш библиотек.

### <a name="synopsis"></a>Краткий обзор

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Аргументы

`PROVIDER`

Используется только с командой `clean`. Указывает кэш поставщика, который нужно очистить. Допустимы следующие значения:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Параметры

Для команды `libman cache` доступны следующие параметры:

* `--files`

  Список кэшируемых файлов.

* `--libraries`

  Список кэшируемых библиотек.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Примеры

* Чтобы просмотреть имена кэшируемых библиотек для каждого поставщика, выполните одну из следующих команд:

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Выходные данные должны выглядеть примерно так:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* Чтобы просмотреть имена кэшируемых файлов библиотек для каждого поставщика, выполните следующую команду:

  ```console
  libman cache list --files
  ```

  Выходные данные должны выглядеть примерно так:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  Обратите внимание, что в представленных выше выходных данных показано, что для поставщика CDNJS кэшируются версии jQuery 3.2.1 и 3.3.1.

* Чтобы очистить кэш библиотек для поставщика CDNJS, выполните следующую команду:

  ```console
  libman cache clean cdnjs
  ```

  После очистки кэша поставщика CDNJS команда `libman cache list` выводит следующее:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* Чтобы очистить кэш для всех поддерживаемых поставщиков, выполните следующую команду:

  ```console
  libman cache clean
  ```

  После очистки кэша всех поставщиков команда `libman cache list` выводит следующее:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Установка глобального средства](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [Репозиторий LibMan на GitHub](https://github.com/aspnet/LibraryManager)
