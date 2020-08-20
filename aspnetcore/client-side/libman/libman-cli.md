---
title: Использование интерфейса командной строки LibMan с ASP.NET Core
author: scottaddie
description: Сведения об использовании интерфейса командной строки LibMan в проекте ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
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
uid: client-side/libman/libman-cli
ms.openlocfilehash: 8b883269a82a1a6e55bf04bd40bfcbab28ae1fb3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625704"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="8b8f6-103">Использование интерфейса командной строки LibMan с ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b8f6-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="8b8f6-104">Автор: [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="8b8f6-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="8b8f6-105">Интерфейс командной строки [LibMan](xref:client-side/libman/index) (LibMan CLI) — это кроссплатформенная программа, которая поддерживается везде, где поддерживается .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8b8f6-106">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="8b8f6-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="8b8f6-107">Установка</span><span class="sxs-lookup"><span data-stu-id="8b8f6-107">Installation</span></span>

<span data-ttu-id="8b8f6-108">Чтобы установить LibMan CLI, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="8b8f6-109">[Глобальное средство .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) устанавливается из пакета NuGet [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/).</span><span class="sxs-lookup"><span data-stu-id="8b8f6-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="8b8f6-110">Чтобы установить LibMan CLI из определенного источника пакета NuGet, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="8b8f6-111">В предыдущем примере глобальное средство .NET Core устанавливается из файла *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* на локальном компьютере Windows.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="8b8f6-112">Использование</span><span class="sxs-lookup"><span data-stu-id="8b8f6-112">Usage</span></span>

<span data-ttu-id="8b8f6-113">После успешной установки CLI можно использовать следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="8b8f6-114">Чтобы узнать установленную версию CLI, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="8b8f6-115">Чтобы просмотреть доступные команды CLI, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="8b8f6-116">Приведенная выше команда выводит результат наподобие следующего:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="8b8f6-117">Доступные команды CLI описываются в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="8b8f6-118">Инициализация LibMan в проекте</span><span class="sxs-lookup"><span data-stu-id="8b8f6-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="8b8f6-119">Команда `libman init` создает файл *libman.json*, если он еще не существует.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="8b8f6-120">Файл создается с содержимым шаблона по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8b8f6-121">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="8b8f6-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="8b8f6-122">Параметры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-122">Options</span></span>

<span data-ttu-id="8b8f6-123">Для команды `libman init` доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="8b8f6-124">Путь относительно текущей папки.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-124">A path relative to the current folder.</span></span> <span data-ttu-id="8b8f6-125">Файлы библиотеки устанавливаются в этом расположении, если в файле *libman.json* свойство `destination` для библиотеки не задано.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="8b8f6-126">Значение `<PATH>` записывается в свойство `defaultDestination` в файле *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="8b8f6-127">Поставщик, который будет использоваться, если поставщик для данной библиотеки не указан.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="8b8f6-128">Значение `<PROVIDER>` записывается в свойство `defaultProvider` в файле *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="8b8f6-129">Замените `<PROVIDER>` одним из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8b8f6-130">Примеры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-130">Examples</span></span>

<span data-ttu-id="8b8f6-131">Чтобы создать файл *libman.json* в проекте ASP.NET Core, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="8b8f6-132">Перейдите в корневой каталог проекта.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-132">Navigate to the project root.</span></span>
* <span data-ttu-id="8b8f6-133">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="8b8f6-134">Введите имя поставщика по умолчанию или нажмите клавишу `Enter`, чтобы использовать поставщик CDNJS по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="8b8f6-135">Допустимы следующие значения:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Команда libman init — поставщик по умолчанию](_static/libman-init-provider.png)

<span data-ttu-id="8b8f6-137">В корневой каталог проекта добавляется файл *libman.json* со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="8b8f6-138">Добавление файлов библиотеки</span><span class="sxs-lookup"><span data-stu-id="8b8f6-138">Add library files</span></span>

<span data-ttu-id="8b8f6-139">Команда `libman install` скачивает и устанавливает файлы библиотеки в проект.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="8b8f6-140">Если файл *libman.json* еще не существует, он создается.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="8b8f6-141">В файле *libman.json* сохраняются данные конфигурации для файлов библиотеки.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8b8f6-142">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="8b8f6-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="8b8f6-143">Аргументы</span><span class="sxs-lookup"><span data-stu-id="8b8f6-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="8b8f6-144">Имя устанавливаемой библиотеки.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-144">The name of the library to install.</span></span> <span data-ttu-id="8b8f6-145">В имени может использоваться нотация номера версии (например, `@1.2.0`).</span><span class="sxs-lookup"><span data-stu-id="8b8f6-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="8b8f6-146">Параметры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-146">Options</span></span>

<span data-ttu-id="8b8f6-147">Для команды `libman install` доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="8b8f6-148">Расположение для установки библиотеки.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-148">The location to install the library.</span></span> <span data-ttu-id="8b8f6-149">Если не указано, используется расположение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-149">If not specified, the default location is used.</span></span> <span data-ttu-id="8b8f6-150">Если в файле *libman.json* свойство `defaultDestination` не указано, этот параметр является обязательным.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="8b8f6-151">Укажите имя файла, который необходимо установить из библиотеки.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="8b8f6-152">Если не указано, устанавливаются все файлы из библиотеки.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="8b8f6-153">Для каждого устанавливаемого файла необходимо задать один параметр `--files`.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="8b8f6-154">Также поддерживаются относительные пути.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-154">Relative paths are supported too.</span></span> <span data-ttu-id="8b8f6-155">Например, `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="8b8f6-156">Имя поставщика, используемого для получения библиотеки.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="8b8f6-157">Замените `<PROVIDER>` одним из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="8b8f6-158">Если значение не указано, используется свойство `defaultProvider` из файла *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="8b8f6-159">Если в файле *libman.json* свойство `defaultProvider` не указано, этот параметр является обязательным.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8b8f6-160">Примеры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-160">Examples</span></span>

<span data-ttu-id="8b8f6-161">Рассмотрим следующий файл *libman.json*:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="8b8f6-162">Чтобы установить файл *jquery.min.js* jQuery версии 3.2.1 в папку *wwwroot/scripts/jquery* с использованием поставщика CDNJS, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="8b8f6-163">Содержимое файла *libman.json* выглядит примерно так:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="8b8f6-164">Чтобы установить файлы *calendar.js* и *calendar.css* из папки *C:\\temp\\contosoCalendar\\* с использованием поставщика файловой системы, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="8b8f6-165">Приведенный ниже запрос появляется по двум причинам:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="8b8f6-166">В файле *libman.json* нет свойства `defaultDestination`.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="8b8f6-167">Команда `libman install` не содержит параметра `-d|--destination`.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![Команда libman install — назначение](_static/libman-install-destination.png)

<span data-ttu-id="8b8f6-169">После принятия назначения по умолчанию содержимое файла *libman.json* будет выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="8b8f6-170">Восстановление файлов библиотек</span><span class="sxs-lookup"><span data-stu-id="8b8f6-170">Restore library files</span></span>

<span data-ttu-id="8b8f6-171">Команда `libman restore` устанавливает файлы библиотеки, определенные в файле *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="8b8f6-172">Действуют следующие правила.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-172">The following rules apply:</span></span>

* <span data-ttu-id="8b8f6-173">Если файла *libman.json* нет в корневом каталоге проекта, возвращается ошибка.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="8b8f6-174">Если для библиотеки указан поставщик, свойство `defaultProvider` в файле *libman.json* игнорируется.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="8b8f6-175">Если для библиотеки указано назначение, свойство `defaultDestination` в файле *libman.json* игнорируется.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8b8f6-176">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="8b8f6-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="8b8f6-177">Параметры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-177">Options</span></span>

<span data-ttu-id="8b8f6-178">Для команды `libman restore` доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8b8f6-179">Примеры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-179">Examples</span></span>

<span data-ttu-id="8b8f6-180">Чтобы восстановить файлы библиотеки, определенные в файле *libman.json*, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="8b8f6-181">Удаление файлов библиотек</span><span class="sxs-lookup"><span data-stu-id="8b8f6-181">Delete library files</span></span>

<span data-ttu-id="8b8f6-182">Команда `libman clean` удаляет файлы библиотек, восстановленные ранее с помощью LibMan.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="8b8f6-183">Папки, которые становятся пустыми после выполнения этой операции, удаляются.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="8b8f6-184">Конфигурации, связанные с файлами библиотек, в свойстве `libraries` файла *libman.json* не удаляются.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8b8f6-185">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="8b8f6-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="8b8f6-186">Параметры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-186">Options</span></span>

<span data-ttu-id="8b8f6-187">Для команды `libman clean` доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8b8f6-188">Примеры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-188">Examples</span></span>

<span data-ttu-id="8b8f6-189">Чтобы удалить файлы библиотек, установленные с помощью LibMan, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="8b8f6-190">Удаление файлов библиотек</span><span class="sxs-lookup"><span data-stu-id="8b8f6-190">Uninstall library files</span></span>

<span data-ttu-id="8b8f6-191">Команда `libman uninstall` делает следующее:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="8b8f6-192">удаляет все файлы, связанные с указанной библиотекой, из назначения в файле *libman.json*;</span><span class="sxs-lookup"><span data-stu-id="8b8f6-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="8b8f6-193">удаляет связанную конфигурацию библиотеки из файла *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="8b8f6-194">В следующих случаях возникает ошибка:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-194">An error occurs when:</span></span>

* <span data-ttu-id="8b8f6-195">файла *libman.json* нет в корневом каталоге проекта;</span><span class="sxs-lookup"><span data-stu-id="8b8f6-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="8b8f6-196">указанная библиотека не существует.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="8b8f6-197">Если установлено несколько библиотек с одним и тем же именем, вам будет предложено выбрать одну из них.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8b8f6-198">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="8b8f6-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="8b8f6-199">Аргументы</span><span class="sxs-lookup"><span data-stu-id="8b8f6-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="8b8f6-200">Имя удаляемой библиотеки.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-200">The name of the library to uninstall.</span></span> <span data-ttu-id="8b8f6-201">В имени может использоваться нотация номера версии (например, `@1.2.0`).</span><span class="sxs-lookup"><span data-stu-id="8b8f6-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="8b8f6-202">Параметры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-202">Options</span></span>

<span data-ttu-id="8b8f6-203">Для команды `libman uninstall` доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8b8f6-204">Примеры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-204">Examples</span></span>

<span data-ttu-id="8b8f6-205">Рассмотрим следующий файл *libman.json*:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="8b8f6-206">Для удаления jQuery можно выполнить любую из следующих команд:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="8b8f6-207">Чтобы удалить файлы Lodash, установленные с помощью поставщика `filesystem`, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="8b8f6-208">Обновление версии библиотеки</span><span class="sxs-lookup"><span data-stu-id="8b8f6-208">Update library version</span></span>

<span data-ttu-id="8b8f6-209">Команда `libman update` обновляет библиотеку, установленную с помощью LibMan, до указанной версии.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="8b8f6-210">В следующих случаях возникает ошибка:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-210">An error occurs when:</span></span>

* <span data-ttu-id="8b8f6-211">файла *libman.json* нет в корневом каталоге проекта;</span><span class="sxs-lookup"><span data-stu-id="8b8f6-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="8b8f6-212">указанная библиотека не существует.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="8b8f6-213">Если установлено несколько библиотек с одним и тем же именем, вам будет предложено выбрать одну из них.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8b8f6-214">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="8b8f6-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="8b8f6-215">Аргументы</span><span class="sxs-lookup"><span data-stu-id="8b8f6-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="8b8f6-216">Имя обновляемой библиотеки.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="8b8f6-217">Параметры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-217">Options</span></span>

<span data-ttu-id="8b8f6-218">Для команды `libman update` доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="8b8f6-219">Получение последней предварительной версии библиотеки.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="8b8f6-220">Получение определенной версии библиотеки.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8b8f6-221">Примеры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-221">Examples</span></span>

* <span data-ttu-id="8b8f6-222">Чтобы обновить jQuery до последней версии, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="8b8f6-223">Чтобы обновить jQuery до версии 3.3.1, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="8b8f6-224">Чтобы обновить jQuery до последней предварительной версии, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="8b8f6-225">Управление кэшем библиотек</span><span class="sxs-lookup"><span data-stu-id="8b8f6-225">Manage library cache</span></span>

<span data-ttu-id="8b8f6-226">Команда `libman cache` управляет кэшем библиотек LibMan.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="8b8f6-227">Поставщик `filesystem` не использует кэш библиотек.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8b8f6-228">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="8b8f6-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="8b8f6-229">Аргументы</span><span class="sxs-lookup"><span data-stu-id="8b8f6-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="8b8f6-230">Используется только с командой `clean`.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-230">Only used with the `clean` command.</span></span> <span data-ttu-id="8b8f6-231">Указывает кэш поставщика, который нужно очистить.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="8b8f6-232">Допустимы следующие значения:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="8b8f6-233">Параметры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-233">Options</span></span>

<span data-ttu-id="8b8f6-234">Для команды `libman cache` доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="8b8f6-235">Список кэшируемых файлов.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="8b8f6-236">Список кэшируемых библиотек.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8b8f6-237">Примеры</span><span class="sxs-lookup"><span data-stu-id="8b8f6-237">Examples</span></span>

* <span data-ttu-id="8b8f6-238">Чтобы просмотреть имена кэшируемых библиотек для каждого поставщика, выполните одну из следующих команд:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="8b8f6-239">Выходные данные должны выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="8b8f6-240">Чтобы просмотреть имена кэшируемых файлов библиотек для каждого поставщика, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="8b8f6-241">Выходные данные должны выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="8b8f6-242">Обратите внимание, что в представленных выше выходных данных показано, что для поставщика CDNJS кэшируются версии jQuery 3.2.1 и 3.3.1.</span><span class="sxs-lookup"><span data-stu-id="8b8f6-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="8b8f6-243">Чтобы очистить кэш библиотек для поставщика CDNJS, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="8b8f6-244">После очистки кэша поставщика CDNJS команда `libman cache list` выводит следующее:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="8b8f6-245">Чтобы очистить кэш для всех поддерживаемых поставщиков, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="8b8f6-246">После очистки кэша всех поставщиков команда `libman cache list` выводит следующее:</span><span class="sxs-lookup"><span data-stu-id="8b8f6-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="8b8f6-247">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="8b8f6-247">Additional resources</span></span>

* [<span data-ttu-id="8b8f6-248">Установка глобального средства</span><span class="sxs-lookup"><span data-stu-id="8b8f6-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="8b8f6-249">Репозиторий LibMan на GitHub</span><span class="sxs-lookup"><span data-stu-id="8b8f6-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
